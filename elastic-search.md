curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg

echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list

sudo apt update

sudo apt install elasticsearch

sudo vim  /etc/elasticsearch/elasticsearch.yml



network.host: localhost 


sudo systemctl start elasticsearch

sudo systemctl enable elasticsearch

curl -X GET "localhost:9200"

sudo systemctl status elasticsearch.service


sudo apt install kibana

sudo vim /etc/kibana/kibana.yml

service.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"] 

sudo systemctl enable kibana

systemctl start kibana
