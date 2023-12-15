«Что такое DevOps. СI/СD» Брюхов А SYS-26

Задание 1
Что нужно сделать:

Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.
Установите на машину с jenkins golang.
Используя свой аккаунт на GitHub, сделайте себе форк репозитория. В этом же репозитории находится дополнительный материал для выполнения ДЗ.
Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта go test . и docker build ..
В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

Решение 1
Установка Jenkins
1. Обновил пакеты системы:
sudo apt update

2.Установил Java Development Kit (JDK). Jenkins требует Java для работы:
sudo apt install openjdk-11-jdk

3.Добавил репозиторий Jenkins и установил его:
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update 
sudo apt install jenkins

4.Запустил службу Jenkins:
sudo systemctl start jenkins

5.Включил автозапуск Jenkins при старте системы:
sudo systemctl enable jenkins

6.Проверил статус Jenkins:
sudo systemctl status jenkins

7.Открыл веб-браузер по адресу http://192.168.6.134:8080. Первоначальный пароль в /var/lib/jenkins/secrets/initialAdminPassword на сервере.

Установка  golang
1. Загрузил архив с https://go.dev/doc/install
2. Извлек загруженный архив в /usr/local:
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf /home/andrebro242/Загрузки/go1.21.5.linux-amd64.tar.gz
3. Добавил /usr/local/go/bin в переменную среды PATH (файл ~/.profile ).Если установка системная то sudo nano /etc/profile:
nano ~/.profile
export PATH=$PATH:/usr/local/go/bin
4.Активирую измененную переменную PATH:
source ~/.profile
5.Проверяю корректность установки:
go version

6.Создание Freestyle Project:https://localhost:8080

  1.создал New Item и дал название "MyGoProject"
  2.настройки проекта: 
Выбор ветки и настройка исходного кода (Source Code Management):
        В разделе "Управление исходным кодом" выбрал "Git".
        Указал URL форка репозитория на GitHub.

Триггеры сборки (Build Triggers):
В разделе "Триггеры сборки" выбрал "GitHub hook trigger for GITScm polling". Это позволит Jenkins запускать сборку 
каждый раз, когда происходят изменения в репозитории на GitHub.


Шаги сборки (Build):
В разделе "Шаги сборки" выбрал "Выполнить команду shell" 
В поле "Команда" указал команды для сборки проекта и выполнения тестов:

export PATH=$PATH:/usr/local/go/bin
/usr/local/go/bin/go test .
/usr/local/go/bin/go build

Сохранил настройки проекта.

7.сохранил изменения и запустил сборку

![Задание 1](решение1.png)

Решение 2
    В Jenkins http://192.168.6.134:8080.

1.Новый pipeline проект:
 "New Item" на главной странице Jenkins.
  Название"MyGoPipeline" и выбрал "Pipeline" в качестве типа проекта

  В разделе "Pipeline", выбрал "Pipeline script" 

    Вставил следующий код в поле "Script":

pipeline {
    // Задаем агента, который будет использоваться для выполнения Pipeline
    agent any
    
    // Определяем переменные среды, необходимые для Go
    environment {
        GOROOT = "/usr/local/go"
        PATH = "$GOROOT/bin:$PATH"
        GO111MODULE = 'on'
    }

    // Определяем этапы выполнения Pipeline
    stages {
        // Этап Checkout: Получение исходного кода из репозитория
        stage('Checkout') {
            steps {
                script {
                    // Используем GitSCM для проверки кода из репозитория
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[url: 'https://github.com/andrebro242/https-github.com-andrebro242-8-02.md.git']]
                    ])
                }
            }
        }

        // Этап Build and Test: Сборка и тестирование проекта на Go
        stage('Build and Test') {
            steps {
                script {
                    // Выводим версию Go
                    sh 'go version'
                    
                    // Выполняем шаги сборки и тестирования проекта
                    sh 'go test .'
                    sh 'go build'
                }
            }
        }
    }

    // Пост-обработка после завершения Pipeline
    post {
        // Действия в случае успешного завершения Pipeline
        success {
            echo 'Сборка прошла успешно! Выполните необходимые действия.'
        }
        // Действия в случае неудачного завершения Pipeline
        failure {
            echo 'Сборка не удалась! Проанализируйте ошибки и внесите необходимые изменения.'
        }
    }
}
    
 Сохранил изменения.

2.Запустил сборку, нажав на кнопку "Build Now" на странице проекта.

Этот pipeline включает три этапа: установка Java и Jenkins, установка Golang, и создание Freestyle проекта. 


![Задание 2](решение21.png)
