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

      curl http://<controller-ip>:8080//wm/core/switch/all/role/json
-> Retorna todas as regras aplicadas aos switchs conectados

      curl http://<controller-ip>:8080//wm/core/switch/all/role/json {"role":"<nova_regra>"} nova_regra: "MASTER", "SLAVE", or "EQUAL"
-> Atribui a regra <nova_regra> aos switchs conectados

Substituindo o **all** pelo ID específico do switch, os dois ultimos comandos retornam o contexto único:

      curl http://<controller-ip>:8080/wm/core/switch/<switchId>/role/json

      curl http://<controller-ip>:8080/wm/core/switch/<swiitchId>/role/json {"role":"<nova_regra>"} nova_regra: "MASTER", "SLAVE", or "EQUAL"

#### Estabelecendo regras para comunicação de portas:

Adicionando um fluxo estático:

      curl -X POST -d '{"switch":"00:00:00:00:00:00:00:01", "name":"flow-mod-1", "cookie":"0", "priority":"32768", "in_port":"1","active":"true", "actions":"output=2"}' http://<controller_ip>:8080/wm/staticentrypusher/json
-> Neste exemplo permite inserir um fluxo no switch que recebe pacotes da porta 1 e os envia na porta 2, você pode compor a string JSON e simplesmente usar um comando curl para enviar o HTTP POST para o controlador. O segundo comando exibe o fluxo definido.

      curl http://<controller_ip>:8080/wm/staticentrypusher/list/00:00:00:00:00:00:00:01/json
      curl http://<controller_ip>:8080/wm/staticentrypusher/list/all/json
-> Exibe todas as entradas estáticas da API referente a um switch ou a todos

      curl http://<controller-ip>:8080/wm/staticentrypusher/clear/all/json
-> Remoção de todas as regras de todos os switchs controlados por este controlador

      curl -X DELETE -d '{"name":"flow-mod-1"}' http://<controller_ip>:8080/wm/staticentrypusher/json
-> Remoção de uma regra de fluxo específica.


#### Obtendo Estatísticas

      curl -X POST -d '' http://<controller-ip>:8080/wm/statistics/config/enable/json
-> Habilita as estatisticas

      curl -X POST -d '' http://<controller-ip>:8080/wm/statistics/config/disable/json
-> Desabilita as estatisticas

      http://<controller-ip>:8080/wm/statistics/bandwidth/<switchId>/<portId>/json
-> Estatísticas de uma porta de um switch específico.

Se deseja obter estatisticas e detalhes, utilize as opções, tanto para todos como para um switch específico:

      curl http://<controller-ip>:8080/wm/core/switch/all/<tipo_de_Estatística>/json  

           "aggregate",
           "desc",
           "experimenter",
           "flow",
           "flow-lightweight",
           "group",
           "group-desc",
           "group-features",
           "meter",
           "meter-config",
           "meter-features",
           "port",
           "port-desc",
           "queue",
           "queue-desc",
           "table",
           "table-desc",
           "table-features",
           "flow-monitor",
           "controller-status",
           "bundle-features",
           "features"
---


---
### Fontes +

[Fluxos estáticos](https://floodlight.atlassian.net/wiki/spaces/floodlightcontroller/pages/1343518/Static+Entry+Pusher+API) | [link2]()| [link3]()
