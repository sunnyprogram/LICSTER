pipeline {
    agent any

    stages {
        // stage('Pytest on Python 3.6') {
        //     agent {
        //         dockerfile {
        //             filename 'Pytest_Python.build'
        //             dir 'projects/JenkinsPipeline/Dockerfiles'
        //             args '--volume jenkins-pytest:/var/jenkins_home --user 0:0'
        //         }
        //     }
        //     steps {
        //         catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //             sh 'pip install -e devices/hmi/software'
        //             sh 'cd devices/hmi/software; pytest'
        //             sh 'pip uninstall LICSTERhmi -y'
        //             sh 'find . -user root -name \'*\' | xargs chmod ugo+rw'
        //         }
        //     }
        // }
        // stage('Python Linting') {
        //     parallel {
        //         stage('Python 3.6') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_6.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-linting:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics'
        //                     sh 'flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics'
        //                 }
        //             }
        //         }

        //         stage('Python 3.7') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_7.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-linting:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics'
        //                     sh 'flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics'
        //                 }
        //             }
        //         }

        //         stage('Python 3.8') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_8.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-linting:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics'
        //                     sh 'flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics'
        //                 }   
        //             }
        //         }
        //     }
        // }
        // stage('Python Bandit') {
        //     parallel{
        //         stage('Python 3.6') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_6.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-bandit:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'bandit -r ./ -x ./devices/hmi/software/tests/'
        //                 }
        //             }
        //         }

        //         stage('Python 3.7') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_7.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-bandit:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'bandit -r ./ -x ./devices/hmi/software/tests/'
        //                 }
        //             }
        //         }

        //         stage('Python 3.8') {
        //             agent {
        //                 dockerfile { 
        //                     filename 'Python_3_8.build'
        //                     dir 'projects/JenkinsPipeline/Dockerfiles'
        //                     args '--volume jenkins-bandit:/var/jenkins_home'
        //                 }
        //             }
        //             steps {
        //                 catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //                     sh 'bandit -r ./ -x ./devices/hmi/software/tests/'
        //                 }   
        //             }
        //         }
        //     }
        // }

        // stage('Flawfinder') {
        //     agent {
        //         dockerfile {
        //             filename 'Flawfinder.build'
        //             dir 'projects/JenkinsPipeline/Dockerfiles'
        //             args '--volume jenkins-flawfinder:/var/jenkins_home'
        //         }
        //     }
        //     steps {
        //         catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //             sh 'flawfinder devices/remote_io/software/Src/'
        //         }
        //     }
        // }
        // stage('RATS') {
        //     agent {
        //         dockerfile {
        //             filename 'RATS.build'
        //             dir 'projects/JenkinsPipeline/Dockerfiles'
        //             args '--volume jenkins-rats:/var/jenkins_home'
        //         }
        //     }
        //     steps {
        //         catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
        //             sh 'rats --resultsonly -w 3 devices/remote_io/software/Src/'
        //         }
        //     }
        // }
        stage('Zapproxy') {
            agent {
                dockerfile {
                    filename 'Zapproxy.build'
                    dir 'projects/JenkinsPipeline/Dockerfiles'
                    args '--volume jenkins-rats:/var/jenkins_home'
                }
            }
            steps {
                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    sh '''
                        cd ./devices/hmi/software
                        mkdir instance
                        cp test/app.sqlite instance/app.sqlite
                        SECRET="0t0RMYslWW5g^QIrwQsOpMz9tvydonZ8"
                        export SECRET_KEY=$SECRET
                        echo $SECRET_KEY > secret.key
                        gunicorn -w 4 -b 0.0.0.0:8080 "app:create_app()" &
                        docker run -t owasp/zap2docker-stable zap-baseline.py -t http://$(ip -f inet -o addr show docker0 | awk '{print $4}' | cut -d '/' -f 1):8080
                    '''
                }
            }
        }
    }
}
