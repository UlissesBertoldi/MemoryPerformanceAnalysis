 # README #

Este README normalmente documentaria quaisquer etapas necessárias para que seu aplicativo seja executado.

### Para que é esse repositório? ###

Esta reposição contém a implementação de um experimento de análise comparativa / desempenho em sistemas de cache distribuídos, Hazelcast e Infinispan, usando o framework Yardstick.

A principal contribuição é que o desenvolvimento de scripts em cima do padrão para realizar benhmarking como o número de clientes (cada um com sua própria conexão) varia.

### Como faço para configurar? ###

#### Configuração do ambiente ####

Precisamos configurar duas PCs com as especificações descritas no capítulo de design da seguinte maneira:

* Execute o primeiro PC que representa os clientes.
* Execute o segundo PC que representa o servidor executando o cluster de cache.
* Configure o ambiente JDK nos 2 PCs.
* Instale o eclipse para Java EE em ambas as máquinas.
* Confira o projeto deste repo.
* Escolha o ramo master_yardstick.
* Configure uma rede ponto a ponto com um cabo cruzado direto. Atribua IPaddresses 10.0.0.2/8 e 10.0.0.1/8 a ambos os PCs, de forma repsectiva.
* Abra as seguintes portas na máquina do servidor: 5701 a 5705 para servidores hazelcast e 11222, 11322, 11422 e 11522 para servidores infinispan.
* Uma classe de configuração chamada GeneralArguments contém configurações principais, incluindo o IP do servidor, tamanho de dados e limite de idade usado em consultas personalizadas.

#### Hazelcast Setup ####

Nesta seção, mostramos as etapas para executar os benchmarks para hazelcast. Primeiro precisamos configurar o servidor, então nós configuramos os clientes.

##### Servidores em execução

Depois de verificar o projeto conforme descrito anteriormente, você deve ser capaz de construir o projeto normalmente. Para executar os servidores, siga estas etapas:

* Especifique o tamanho de dados na classe GeneralArguments para um valor inicial.
* Construa o projeto usando o plugin maven com o pacote limpo de alvos.
* Abra a interface da linha de comando (cmd) e altere o diretório para onde o projeto existe.
* Emita este comando: bin \ benchmark-manual-servers-start.bat. Este comando executará os servidores hazelcast (4 nós) e um cache * distribuído será criado e inicializado com os dados.
* Depois de executar os clientes conforme descrito a seguir, mate os servidores e repita a partir do passo 1 para definir o tamanho de dados como um novo valor de tamanho de dados.

##### Clientes em execução

Depois de fazer os servidores funcionando, precisamos agora executar os benchmarks da seguinte maneira:

* Construa o projeto com o maven com o pacote limpo de alvos.
* Abra o arquivo bin \ benchmark-bootstrap.sh, então vá para a linha 86 e, em seguida, mude os valores -Xms e -Xmx para 128m e 256m, respectivamente.
* Abra o bin \ benchmark-manual-drivers-start.sh, então vá para as linhas 91 e 100 e, em seguida, adicione% N a% H% M% S.
* Abra o terminal e altere o diretório para onde o projeto existe.
* Emita o seguinte comando: ./run_clients2.sh Hazelcast 5000. Passamos para este comando o nome do sistema (ou seja, Hazelcast neste caso) eo tamanho de dados do cache distribuído. Este comando executará os benchmarks para diferentes tipos de consultas (get, custom, custom-complex) e para diferentes números de clientes (1, 4, 16, ... etc.). Ele irá desencadear o script monitor.sh, além de gerenciar a criação e o movimento de arquivos de resultados.
* Repita a partir da etapa 5, reinicie os servidores com novo tamanho de dados e altere o tamanho de dados para outro tamanho de dados.

#### Infinispan Setup ####

Nesta seção, mostramos as etapas para executar os benchmarks para Infinispan. Primeiro precisamos configurar o servidor, então nós configuramos os clientes.

##### Servidores em execução

A execução de servidores Infinispan é um pouco diferente. Você precisa instalar o tempo de execução do servidor Infinispan em seu site (http://infinispan.org/download/). A versão usada nesta experiência é 8.2.2.final. Para executar os servidores, siga estas etapas:

* Abra o cmd e altere o diretório para onde a pasta do servidor Infinispan existe.
* Vá para a pasta bin e emita os seguintes comandos, cada um em cmd separado:

Standalone.bat -c clustered.xml -Djboss.node.name = nodeA

Standalone.bat -c clustered.xml -Djboss.node.name = nodeB -Djboss.socket.binding.port-offset = 100

Standalone.bat -c clustered.xml -Djboss.node.name = nodeC -Djboss.socket.binding.port-offset = 200

Standalone.bat -c clustered.xml -Djboss.node.name = nodeD -Djboss.socket.binding.port-offset = 300

* Especifique o tamanho dos dados na classe GeneralArguments para o tamanho inicial dos dados.
* Para inicializar os dados, temos dois programas: um para obter consulta e outro para consultas personalizadas.
* Quando benchmarking get query, execute a classe ServerSimpleDataInitializer. (Precisa reiniciar servidores)
* Ao comparar pesquisas personalizadas, execute a classe ServerCustomDataInitializer. (Precisa reiniciar servidores)
* Depois de executar os clientes conforme descrito a seguir, mate os servidores e repita a partir do passo 1, mas com tamanho de dados de outro tamanho de dados.

##### Clientes em execução

Depois de fazer os servidores funcionando, precisamos agora executar os benchmarks da seguinte maneira:

* Construa o projeto com o maven com o pacote limpo de alvos.
* Abra o arquivo bin \ benchmark-bootstrap.sh, então vá para a linha 86 e, em seguida, mude os valores -Xms e -Xmx para 128m e 256m, respectivamente.
* Abra o bin \ benchmark-manual-drivers-start.sh, então vá para as linhas 91 e 100 e, em seguida, adicione% N a% H% M% S.
* Abra o terminal e altere o diretório para onde o projeto existe.
* Temos dois scripts no lado do cliente:

** Quando benchmarking get query: Emita o seguinte comando: ./run_clients_infinispan_get.sh Infinispan 5000. Passamos para este comando o nome do sistema (ou seja, Infinispan neste caso) eo tamanho de dados do cache distribuído. Este comando executará o benchmark para obter consulta e para diferentes números de clientes (1, 4, 16). Ele irá desencadear o script monitor.sh, além de gerenciar a criação e o movimento de arquivos de resultados.
** Quando benchamrking consultas personalizadas: emita o seguinte comando:

./run_clients_infinispan_custom.sh Infinispan 5000. Este comando executará os benchmarks para consultas personalizadas e custom-complexas e para diferentes números de clientes (1, 4, 16). Ele irá desencadear o script monitor.sh, além de gerenciar a criação e o movimento de arquivos de resultados.

* Repita a partir da etapa 5, reinicie os servidores com novo tamanho de dados e altere o tamanho de dados para outro tamanho de dados.

### Diretrizes de contribuição ###

* Desenvolver scripts no topo do critério para realizar benhmarking à medida que o número de clientes (cada um com sua própria conexão) varia.
* Realização de um experimento de análise de desempenho controlado em Hazelcast e Yardstick.
