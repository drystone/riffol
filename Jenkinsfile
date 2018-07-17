pipeline {
    agent none
    stages {
        stage("Distros") {
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
                        sh 'echo ${env.CARGO}'
                        sh """
                                $CARGO clean
                                $CARGO update
                                $CARGO test
                                $CARGO build --release
                        """
                        sh '''
                                LIBC_VERSION=$(ldd --version | sed -r 's/(.* )//')
                                mkdir -p assets
                                tar -C target/release -czf assets/riffol-$LIBC_VERSION.tar.gz riffol
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
                        sh "${env.CARGO} clean && ${env.CARGO} update && ${env.CARGO} test"
                        sh "${env.CARGO} build --release"
                        sh "mkdir -p assets && cd target/release && tar -czf ../../assets/riffol-$LIBC_VERSION.tar.gz riffol"
                    }
                }
            }
        }
    }
}