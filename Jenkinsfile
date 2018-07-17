pipeline {
    agent none
    stages {
        stage("Test and Build") {
            environment {
                CARGO = "~/.cargo/bin/cargo"
                BINARY = "target/release/bin/riffol"
            }
            stages {
                stage("Debian") {
                    agent {
                        dockerfile {
                            dir "ci/debian"
                        }
                    }
                    steps {
                        sh '''
                            LIBC_VERSION=$(ldd --version | head -n1 | sed -r 's/(.* )//')
                            mkdir -p assets
                            cp README.md assets/riffol-$LIBC_VERSION.tar.gz
                        '''
                    }
                }
                stage("CentOS") {
                    agent {
                        dockerfile {
                            dir "ci/centos"
                        }
                    }
                    steps {
                        sh '''
                            LIBC_VERSION=$(ldd --version | head -n1 | sed -r 's/(.* )//')
                            mkdir -p assets
                            cp README.md assets/riffol-$LIBC_VERSION.tar.gz
                        '''
                    }
                }
            }
        }
        stage("Upload Assets") {
            steps {
                sh "ci/release.sh drystone/riffol"
            }
        }
    }
}