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
                        sh script: 'echo [$LIBC_VERSION]',
                        sh script: """
                                $CARGO clean
                                $CARGO update
                                $CARGO test
                                $CARGO build --release
                        """
                        sh script: '''
                                mkdir -p assets
                                LIBC_VERSION=1.2
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