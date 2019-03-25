# Configurar o Open vSwitch com Controller

_Este Tutorial tem como objetivo trazer os comandos utilizados na configuração do **Open vSwitch + Controller**, juntamente com o controlador escolhido gerenciar a rede criando novas configurações definindo padrões de fluxo e recuperando informações, como tais procedimentos podem ser realizados remotamente temos um ganho relevante no controle de redes globalmente distribuídas._
### O que foi utilizado
Será utilizado um controlador **Floodligth** e o **Mininet** para emular uma rede com 1 switch Open vSwitch (__s1__) + 4 hosts (__h1, h2, h3, h4__).

### Como Acessar
Tanto com os comandos do controlador (fornecidos por API) como os que consomem dados do OVSDB necessitam passar como parâmetro o caminho do servidor, no caso do controlador usa-se o endereço [ip]:

      curl http://<controller-ip>:8080/wm/core/controller/switches/json
-> Este Exemplo lista os switches conectados ao controlador

Já no caso do OVSDB seja localmente ou remoto necessita direcionar com o protocolo e a porta relativa ao **Manager** utilizando o comando **ovs-vsctl**(possibilita a administração do switch em modo OpenFlow):

      ovs-vsctl --db=<protocolo>:<porta> comando

### Usando Curl com Floodligth REST API

curl http://<controller-ip>:8080/

No Floodligth o acesso ao controlador pode ser totalmente gerenciado utilizando __Curl__.

      curl http://<controller-ip>:8080/wm/device/
-> Lista todos dispositivos conectados ao controlador, retorna um JSON.

Para ajudar na visualização dos dados obtidos podemos usar um conversor JSON, que fará a organização do resultado:

      curl http://<controller-ip>:8080/wm/core/controller/switches/json | python -m json.tool
-> Retorna o endereço ao qual os switches estão endereçados, a versão do OpenFlow, e a identificação.

      curl http://<controller-ip>:8080/wm/core/memory/json  
-> Retorna a memória utilizada pelo controlador

      curl http://<controller-ip>:8080/wm/core/health/json
-> Status e integridade da REST API

      curl http://<controller-ip>:8080/wm/core/version/json
-> Versão e nome

      curl http://<controller-ip>:8080/wm/core/system/uptime/json
-> Tempo em atividade

      curl http://<controller-ip>:8080/wm/core/storage/tables/json
-> Retorna as tabelas contidas no armazenamento

      curl http://<controller-ip>:8080/wm/core/role/json
->

      curl http://<controller-ip>:8080//wm/core/switch/all/role/json
-> Retorna todas as regras aplicadas a os switchs conectados

      curl http://<controller-ip>:8080/
