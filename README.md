Olá professor, nós somos o grupo N1_CLUSTERMONGODB 6

Aqui estão todos os comandos utilizados no video - 

Pra começar, vamos criar a Criação da Rede Docker uma rede Docker chamada mongoCluster foi criada para permitir que os contêineres se comuniquem entre si.

docker network create mongoCluster

1. Iniciar o Contêiner mongo10
Foi iniciado o primeiro contêiner MongoDB, mapeado para a porta 27017, com o nome mongo10. Esse contêiner é o primeiro nó do conjunto de réplicas.

docker run -d --rm -p 27017:27017 --name mongo10 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo10

docker run -d --rm -p 27018:27017 --name mongo20 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo20

docker run -d --rm -p 27019:27017 --name mongo30 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo30

docker run -d --rm -p 27020:27017 --name mongo40 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo40

2.  Conectar ao MongoDB no Contêiner mongo10
Foi realizada a conexão ao MongoDB no contêiner mongo10 para rodar comandos de inicialização do conjunto de réplicas.

mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.3.8

3. Testar a Conexão com o MongoDB
Executou-se o comando db.runCommand({hello: 1}) para verificar a conexão com o MongoDB.

db.runCommand ({hello:1})

4.  Iniciar o Conjunto de Réplicas
Foi executado o comando rs.initiate() para iniciar o conjunto de réplicas, com os 4 nós definidos.

rs.initiate ({ _id: "myReplicaSet", members:[{_id:0, host: "mongo10"}, {_id:1, host: "mongo20"}, {_id:3, host: "mongo30, {_id:4, host: "mongo40,"}]})

5. Sair do MongoDB
Após a execução do comando, foi utilizado o comando exit para sair da interface mongosh dentro do contêiner mongo10.

exit

10. Verificar Status do Conjunto de Réplicas no mongo10
Foi executado o comando rs.status() para verificar o status do conjunto de réplicas no contêiner mongo10.

docker exec -it mongo10 mongosh --eval "rs.status()"

11. Verificar o Nó Primário
Foi verificado qual nó está configurado como primário no conjunto de réplicas.

rs.isMaster().primary

12. Inserir Dados no Banco de Dados Prod
Foram inseridos dados na coleção cliente do banco de dados Prod, com múltiplos registros.

use Prod
db.cliente.insertOne({codigo: 1, nome: "Ana Maria"})
db.cliente.insertOne({codigo: 2, nome: "Maria Jose"})
db.cliente.insertOne({codigo: 3, nome: "Jose Silva"})
db.cliente.insertOne({codigo: 4, nome: "Luis Souza"})
db.cliente.insertOne({codigo: 5, nome: "Fernanda Silva"})

13. Consultar Dados Inseridos
Foi realizada uma consulta para verificar os dados inseridos na coleção cliente.

db.cliente.find()

14. Parar o Contêiner mongo10
Foi parado o contêiner mongo10 para testar a resiliência do conjunto de réplicas.

docker stop mongo10

15. Verificar o Status do Conjunto de Réplicas no mongo20
Após parar o contêiner mongo10, foi verificado o status do conjunto de réplicas no contêiner mongo20 para garantir que o nó primário continuava ativo.

docker exec -it mongo20 mongosh --eval "rs.status()"


16. Inserir Dados no Contêiner mongo20
Foram inseridos novos dados no banco de dados Prod no contêiner mongo20.

db.cliente.insertOne({codigo: 6, nome: "Bruno Souza"})

17. Consultar Dados no Contêiner mongo20
Foi feita uma consulta para verificar se os dados inseridos estavam disponíveis no contêiner mongo20.

use Prod
db.cliente.findOne()

18. Inserir Dados no Contêiner Primário (Após Falha)
Após a falha de mongo10, a inserção de dados foi realizada novamente, desta vez conectando ao nó primário automaticamente.

db.cliente.insertOne({codigo: 7, nome: "Davi Cardoso"})

19. Reiniciar o Contêiner mongo10
Foi reiniciado o contêiner mongo10 para verificar como o conjunto de réplicas se comportaria após a reintegração de um nó.

docker run -d --rm -p 27017:27017 --name mongo10 --network mongoCluster mongodb/mongodb-community-server:latest --replSet myReplicaSet --bind_ip localhost,mongo10

20. Verificar o Status Após Reinício do Contêiner mongo10
Após o reinício do contêiner mongo10, foi verificado o status do conjunto de réplicas no contêiner mongo20.

docker exec -it mongo20 mongosh --eval "rs.status()"
