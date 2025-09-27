Local Elasticsearch
=================


# Elasticsearch Containers

Starting a local test environment of Elasticsearch and Kibana:

```
mgarcia@PC-KL-26743:~$
mgarcia@PC-KL-26743:~$
mgarcia@PC-KL-26743:~$ curl -fsSL https://elastic.co/start-local | sh

  ______ _           _   _
 |  ____| |         | | (_)
 | |__  | | __ _ ___| |_ _  ___
 |  __| | |/ _` / __| __| |/ __|
 | |____| | (_| \__ \ |_| | (__
 |______|_|\__,_|___/\__|_|\___|
-------------------------------------------------
🚀 Run Elasticsearch and Kibana for local testing
-------------------------------------------------

ℹ️  Do not use this script in a production environment

⌛️ Setting up Elasticsearch and Kibana v9.0.3...

- Generated random passwords
- Created the elastic-start-local folder containing the files:
  - .env, with settings
  - docker-compose.yml, for Docker services
  - start/stop/uninstall commands
- Running docker compose up --wait

[+] Running 25/25
 ✔ kibana_settings Pulled                                                                                                                            68.4s
   ✔ 5da0776917bf Pull complete                                                                                                                       5.2s
   ✔ 739d054ed0a2 Pull complete                                                                                                                       5.3s
   ✔ ed8ac5ccea5b Pull complete                                                                                                                       5.3s
   ✔ 7aac8caba3fc Pull complete                                                                                                                       5.4s
   ✔ 4ca545ee6d5d Pull complete                                                                                                                      85.2s
   ✔ caa254e14350 Pull complete                                                                                                                      62.0s
   ✔ 2f6873db9e00 Pull complete                                                                                                                      62.0s
   ✔ 5ffa425898eb Pull complete                                                                                                                      62.0s
   ✔ 57887820b424 Pull complete                                                                                                                      62.1s
   ✔ 23e010ff0573 Pull complete                                                                                                                      62.1s
 ✔ kibana Pulled                                                                                                                                     91.7s
   ✔ 90dad6020a52 Pull complete                                                                                                                      11.1s
   ✔ 61a3e6bbecb5 Pull complete                                                                                                                      84.6s
   ✔ b0e1d9e367dd Pull complete                                                                                                                      84.6s
   ✔ efe72e4aa6ac Pull complete                                                                                                                      84.7s
   ✔ ea2718f611a6 Pull complete                                                                                                                      84.8s
   ✔ bd136fc9f90c Pull complete                                                                                                                      84.8s
   ✔ 3612f984425a Pull complete                                                                                                                      84.8s
   ✔ 4c45a31a8ef2 Pull complete                                                                                                                      84.9s
   ✔ 2150d187a361 Pull complete                                                                                                                      84.9s
   ✔ 4fe1c936891e Pull complete                                                                                                                      84.9s
   ✔ 390e02417b64 Pull complete                                                                                                                      84.9s
   ✔ 2295be7248a1 Pull complete                                                                                                                      85.0s
 ✔ elasticsearch Pulled                                                                                                                              68.4s
[+] Running 6/6
 ✔ Network elastic-start-local_default             Created                                                                                            0.1s
 ✔ Volume "elastic-start-local_dev-elasticsearch"  Created                                                                                            0.0s
 ✔ Volume "elastic-start-local_dev-kibana"         Created                                                                                            0.0s
 ✔ Container es-local-dev                          Healthy                                                                                           34.5s
 ✔ Container kibana_settings                       Exited                                                                                            33.7s
 ✔ Container kibana-local-dev                      Healthy                                                                                           56.7s

🎉 Congrats, Elasticsearch and Kibana are installed and running in Docker!

🌐 Open your browser at http://localhost:5601

   Username: elastic
   Password: wSKaczHL

🔌 Elasticsearch API endpoint: http://localhost:9200
🔑 API key: YXI0MXBwY0JLMlhWcjVpRnZBeXM6aDVTc0ZVb0xEdFlZZEVmQnRlWFZxdw==


Learn more at https://github.com/elastic/start-local

mgarcia@PC-KL-26743:~$ ls
Documents  Downloads  elastic-start-local  Work
mgarcia@PC-KL-26743:~$
```

# Next Steps

Documentation of next steps: [https://www.elastic.co/docs/solutions/search/elasticsearch-basics-quickstart](https://www.elastic.co/docs/solutions/search/elasticsearch-basics-quickstart)

Elasticsearch Local Github: [https://github.com/elastic/start-local](https://github.com/elastic/start-local)

Jupyter notebooks to learn Elasticsearch: [https://github.com/elastic/elasticsearch-labs/tree/main/notebooks#readme](https://github.com/elastic/elasticsearch-labs/tree/main/notebooks#readme)
