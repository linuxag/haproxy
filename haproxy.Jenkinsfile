def git_url = 'https://github.com/linuxag/haproxy.git'

pipeline
{
    agent {
        label 'haproxy'
    }
     options{
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    stages
    {
        stage('copy-config-file')
        {
            steps
            {
            sh '''
            cd haproxy
            echo "" >> haproxy.cfg
            sudo cp -r . /opt/haproxy
            '''
            }
        }
        stage('check-haproxy-config')
        {
            steps
            {
            sh '''
            sudo docker run --rm -i -v /usr/local/etc/haproxy/ssl:/usr/local/etc/haproxy/ssl -v /opt/haproxy:/usr/local/etc/haproxy haproxy -c -f /usr/local/etc/haproxy/haproxy.cfg
            '''
            }
            
        }
        stage('openvpn-kill')
        {
            steps
            {
                sh '/root/execute.sh' 
                sleep 5
            }
        }
        stage('deploy-haproxy')
        {
            steps
            {
                sh '''
                sudo docker-compose -f /opt/haproxy/haproxy-compose.yml down | exit 0
                sudo docker-compose -f /opt/haproxy/haproxy-compose.yml up -d
                sleep 5
                '''
            }
        }
        stage('status-check')
        {
            steps
            {
                sh '''
                sleep 2
                sudo docker logs haproxy 
                sudo docker ps -a
                '''
            }
        }
    }
}