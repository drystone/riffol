pipeline {
    agent none
    stages {
        stage("Upload Assets") {
            agent any

            steps {
                sh '''
                    echo $OAUTH
                    curl -L -o/usr/bin/jq https://github.com/stedolan/jq/releases/download/jq-1.5/jq-linux64
                    chmod +x /usr/bin/jq
                    ci/release.sh drystone/riffol
                '''
            }
        }
        stage("Test and Build") {
            environment {
                CARGO = "~/.cargo/bin/cargo"
                BINARY = "target/release/bin/riffol"
                OAUTH = credentials("704569ba-d880-401d-86b9-0894c11ec46c")
            }
            stages {
                stage("Debian") {
                    agent {
                        dockerfile {
                            dir "ci/debian"
                        }
                    }
                    steps {
                        sh """
                            $CARGO clean
                            $CARGO update
                            $CARGO test
                            $CARGO build --release
                        """
                        sh '''
                            LIBC_VERSION=$(ldd --version | head -n1 | sed -r 's/(.* )//')
                            mkdir -p assets
                            cp README.adoc assets/riffol-$LIBC_VERSION.tar.gz
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
                        sh '''
                            LIBC_VERSION=$(ldd --version | head -n1 | sed -r 's/(.* )//')
                            mkdir -p assets
                            cp README.adoc assets/riffol-$LIBC_VERSION.tar.gz
                        '''
                    }
                }
            }
        }
    }
}