# Zero to Hero Workshop

Este projeto faz parte de um exercício/workshop projetado para mostrar como começar com o New Relic APM. O objetivo é começar do zero (como baixar/compilar/empacotar um aplicativo) para coletar telemetria, dashboards, alertas, etc. Tudo isso usando a plataforma de observabilidade da New Relic.

Vamos configurar os seguintes componentes New Relic:
New Relic Infrastructure
New Relic APM
New Relic Browser
New Relic Logs

Começaremos instalando o New Relic Infrasctructure Agent para que possamos coletar as informações de CPI/memória/disco do host. Em seguida, clonaremos (baixaremos) um aplicativo java de amostra (Spring PetClinic), compilaremos, empacotaremos e executaremos o aplicativo.

Assim que o aplicativo estiver funcionando, iremos instrumentar o aplicativo Spring PetClinic usando o agente New Relic APM que monitora o serviço gerando traces e métricas que são reportados à plataforma New Relic.

Em seguida, instrumentaremos a interface do usuário final do Spring PetClinic (páginas HTML renderizadas pelo aplicativo) com o agente New Relic Browser que gerará telemetria sobre todos os cliques individuais e carregamentos de página executados pelo usuário final.

Por fim, configuraremos o aplicativo Spring PetClinic para gravar os logs do aplicativo no sistema de arquivos e o New Relic Infrastructure Agent para ler (tail) os logs e relatar para a plataforma New Relic.

Aqui estão as principais etapas ou tarefas deste exercício:
- Preparar VM e Ambiente
- Faça login na plataforma New Relic
- Instalar o agente de infraestrutura da New Relic
- Clonar, compilar e construir o aplicativo PetClinic
- Instalar e configurar o agente APM New Relic
- Instalar e configurar o New Relic Browser Agent
- Configurar logs do PetClinic
- Dashboards


## Pré-requisitos (ou pré-trabalho)
### 1. Ambiente (VM)
O exercício e as instruções abaixo usam uma VM do Ubuntu (18.04) e as instruções para instalar pacotes durante as primeiras etapas são direcionadas às VMs do Ubuntu. Se você planeja usar distribuições diferentes, certifique-se de ter o comando adequado para instalar as dependências.

Se você planeja executar este exercício localmente, em formato de laboratório, recomendamos o Multipass ([https://multipass.run/](https://multipass.run/)) ou o VirtualBox ([https://www. virtualbox.org/](https://www.virtualbox.org/)). Embora não abordemos a criação de VMs aqui, há muitos recursos disponíveis com instruções detalhadas em vários sites. Uma VM com 2 GB, 1 vCPU e 15 GB de HD deve lidar com isso.

A VM precisa ter acesso à Internet para baixar instaladores e enviar a telemetria para os endpoints da New Relic.

### 2. Nova Conta Relíquia
Outro Pré-requisito é o acesso à Plataforma New Relic. Se você trabalha para uma empresa que usa o New Relic, verifique com sua equipe interna para obter acesso à conta.

Caso você queira apenas experimentá-lo, você pode se inscrever para uma conta gratuita (é uma conta perpetuamente gratuita, sem necessidade de cartão de crédito) aqui: [https://newrelic.com/signup](https://newrelic .com/signup)

### 3. Conhecimento básico do console (Shell)
Por fim, este exercício requer conhecimento básico e familiaridade com o uso de um console shell, execução de comandos e edição de arquivos de configuração (estamos usando o vi aqui, você pode usar o editor que preferir). Se você nunca tentou isso, recomendamos ler um pouco sobre o shell do Linux e os comandos/editores antes de iniciar.

## Começando
### Criação de VM
Se você estiver criando sua VM usando o Multipass, poderá executar o seguinte comando:
```
    multipass start -n workshop -c 1 -m 1G -d 10G
```
(cria uma VM chamada 'workshop' com 1CPU, 1Gig de RAM e disco de 10Gig)

### Login de VM
A primeira etapa é fazer login na sua VM. Nas etapas aqui, usaremos os comandos para multipass.

```
    multipass shell workshop
```

### Preparação de VM
 Depois de fazer login na VM, você executará alguns comandos para baixar os componentes necessários:

```
    sudo apt update
    sudo apt install curl git maven openjdk-11-jdk unzip
```
    
Pode levar alguns minutos, dependendo das especificações da VM e da velocidade da rede. Os comandos acima instalarão os componentes necessários para o exercício.

### Primeiro login no New Relic
Enquanto isso, você pode ir em frente e fazer login na sua conta New Relic.

 - https://login.newrelic.com
 
 FOTO
 
E se você não tem uma conta, inscreva-se aqui: https://newrelic.com/signup

Após o login, você deve chegar a uma página como esta:

FOTO


## Workshop

### Nova Infraestrutura Relic
Vamos começar com a etapa 1: **Instalar o New Relic Infrastructure Agent**.

O agente de infraestrutura é responsável tanto por
- Coleta e reportar métricas do host (disco, cpu, memória, etc.)
- Coleta e reportar logs do host e de aplicativos

A New Relic oferece assistentes para orientá-lo na configuração dos agentes e instrumentação. Para chegar ao assistente, clique no ícone do canto superior direito "Add more data"

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-1.png)

Você pode digitar "Linux" na caixa de pesquisa e selecionar a distro que está usando:

![Clique em A
dd Mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-2.png)

Então você seleciona o OS/Distro:

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-3.png)

Você será levado a um assistente onde selecionará algumas opções. As configurações padrão devem funcionar, não há necessidade de fazer alterações.

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-4.png)

O assistente produzirá alguns comandos que precisam ser executados no shell. Aqui está um exemplo:

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-5.png)

```
curl -Ls https://download.newrelic.com/install/newrelic-cli/scripts/install.sh | bash && sudo NEW_RELIC_API_KEY=<X12314125123123123123123> NEW_RELIC_ACCOUNT_ID=999999 /usr/local/bin/newrelic install
```

*(NÃO COPIE E COLE este comando durante o exercício, pois ele não funcionará. Você deve copiar o comando da página do Assistente de instalação do New Relic. O comando acima tem os valores API KEY e ACCOUNT NUMBER REMOVIDOS, e precisamos dos valores reais associado à sua conta)*

Este comando fará download e configurará o New Relic Infrastructure Agent. Quando a instalação estiver concluída, você poderá navegar até a página Infraestrutura para ver os dados do seu host.

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-6.png)

Você pode navegar e ver as informações do seu host clicando em Explorer >> Hosts >> Host Name (workshop)

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/infra-7.png)

Depois de ver os dados fluindo para o seu host, estamos prontos para começar com a configuração do aplicativo

### Configuração da Spring PetClinic

#### Baixe e construa o aplicativo Spring PetClinic
A primeira coisa que precisamos para configurar o APM é... bem, um aplicativo. Para este exercício, usaremos o aplicativo Spring Pet Clinic. Este é um aplicativo java de exmplo muito popular construído com o framework Spring (Springboot).

Agora vamos clonar o repositório do aplicativo, então vamos compilar, construir, empacotar e testar o aplicativo.

```
    git clone https://github.com/spring-projects/spring-petclinic
```
então abrimos o diretório
```
    cd spring-petclinic
```
e execute o comando maven para compilar/compilar/empacotar

```
    ./mvnw pacote -Dmaven.test.skip=true
```
(isso pode levar alguns minutos na primeira vez que você executar, o maven baixará muitas dependências antes de compilar o aplicativo. As execuções futuras serão muito mais rápidas)

Quando a compilação estiver concluída, você poderá executar o aplicativo com o seguinte comando:
```
    java -jar target/spring-petclinic-*.jar
```

Você pode validar se o aplicativo está em execução visitando.

    http://<VM_IP_ADDRESS>:8080

(sinta-se livre para navegar e clicar ao redor)


### Instrumentação APM New Relic
#### Instrumentar o aplicativo com o agente New Relic
Agora que o aplicativo está em execução, é hora de configurar a instrumentação do APM.

Vamos continuar o processo visitando a interface do usuário do New Relic novamente.

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-1.png)

Explorer >> Serviços - APM >> Adicionar mais dados

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-2.png)

Selecione "Java"

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-3.png)

O assistente irá guiá-lo através do download dos arquivos necessários

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-4.png)

A configuração de monitoramento de aplicativos Java requer download manual dos arquivos principais

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-5.png)

O APM Instrumentation Wizard mostrará algumas opções para você selecionar, como nome do aplicativo, ambiente, etc. Neste cenário, estamos usando:
- Nome do aplicativo: petclinic
- Ambiente: conf21

*(verifique se você está no diretório spring-petclinic)*

```
cd ~/spring-petclinic
```

```
curl -O https://download.newrelic.com/newrelic/java-agent/newrelic-agent/current/newrelic-java.zip
```

```
descompacte newrelic-java.zip
```

O agente é altamente configurável e vem com a maior parte da configuração já predefinida, mas há duas propriedades obrigatórias que precisamos configurar:
- Nome da Aplicação
- Chave de licença.

Existem diferentes maneiras de configurar o Agente APM da New Relic e as formas mais populares são: YAML e Propriedades do ambiente. Você pode ver mais detalhes aqui XXXX

##### Propriedades do ambiente
Você pode configurar as seguintes propriedades de ambiente e o agente as selecionará durante a inicialização.
```
export NEW_RELIC_APP_NAME=PetClinic
exportar NEW_RELIC_LICENSE_KEY=AAAA
```
Ou

##### YAML
Você pode configurar o arquivo newrelic.yml e alterar as duas propriedades nele.
```
vim newrelic/newrelic.yml
```
e adicione as linhas

```
License_key: YYYYYY
app_name: PetClinic
```

Por fim, executaremos nosso aplicativo adicionando a tag -javaagent na frente do comando

```
    java -javaagent:./newrelic.jar -jar target/spring-petclinic-*-SNAPSHOT.jar
```

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/apm-6.png)

Vamos visitar nosso aplicativo novamente para gerar algum tráfego.

http://<VM_IP_ADDRESS>:8080

*(clique, gere erros, adicione visitas, etc )*

Em seguida, você pode visitar a interface do usuário do New Relic APM e examinar os componentes do aplicativo, rastreamentos, etc.

IMAGEM?

Explorar >> Serviços >> APM?

--------------------------------------------------
### New Relic Browser (DEM - Monitoramento de Experiência Digital)

Para a instrumentação do usuário final, adicionaremos o snippet do New Relic Browser Javascript Agent (LINK?) nas páginas. Usaremos o assistente novamente.

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/browser-1.png)

O New Relic Browser oferece duas opções diferentes para o método de implantação: Instrumentação Automática Via APM e Copiar-Colar

![Clique em Adicionar mais dados](https://raw.githubusercontent.com/somensari/zerotohero-nr/main/images/browser-2.png)

1) Instrumentação Automática Via APM é a abordagem recomendada onde o Agente APM irá instrumentar as páginas injetando automaticamente o agente javascript nas páginas.

2) Copiar e Colar é realmente o que significa: Você obterá um snippet JS que precisa ser adicionado manualmente às páginas.

O assistente mostrará então um trecho de código HTML que precisa ser colocado no topo das páginas (de preferência na seção < HEAD >).

Você pode manter as opções na seção 2 com valores padrão (Distributed Tracing e Cookie Collection)

(IMAGEM)

A última etapa é nomear o aplicativo. Por termos o aplicativo já instrumentado no APM, você deve conseguir "Pesquisar" e encontrar o PetClinic na lista.

Depois de clicar em "Ativar", um JS Snippet será exibido. Aqui está um exemplo

**************************************************** ***************

O aplicativo Spring PetClinic usa uma única página HTML como a página de "layout" que é reutilizada em todas as páginas do aplicativo. Este é o local perfeito para inserir o snippet do agente do navegador New Relic, pois ele será carregado em todas as páginas automaticamente.

Vamos então editar a página de layout:

```
    vim src/main/resources/templates/fragments/layout.html
```

e vamos inserir o recorte que geramos acima na seção < HEAD > da página.

Agora precisamos reconstruir o aplicativo e executá-lo novamente:
```
    ./mvnw pacote -Dmaven.test.skip=true
    java -javaagent:./newrelic.jar -jar target/spring-petclinic-*-SNAPSHOT.jar
```

Então vamos visitar o aplicativo novamente para gerar mais tráfego, agora devemos ver os rastreamentos RUM sendo relatados.

    http://<VM_IP_ADDRESS>:8080

(sinta-se livre para navegar e clicar ao redor)

Vamos visitar a interface do usuário do New Relic e ver alguns dos rastreamentos e métricas.

Explorar >> Navegador

Você deve ver algumas das métricas de interface do usuário do Spring PetClinic aparecendo na interface do usuário

--------------------------------------------------
### Registros de novas relíquias
Para o componente New Relic Logs, vamos configurar o aplicativo Spring PetClinic para gravar logs em um arquivo no sistema de arquivos e configurar o agente New Relic Infrastructure para ler (cauda) esse arquivo de log e relatar as informações para a plataforma New Relic.


#### Configuração de Logback da Clínica de Animais de Estimação da Primavera
O aplicativo Spring PetClinic pode ser configurado para usar várias bibliotecas de log java diferentes. Neste cenário, estamos usando logback. Vamos criar um arquivo de configuração de logback:

Só precisamos criar um arquivo chamado logback.xml na pasta de configuração.
```
    vim src/main/resources/logback.xml
```

e adicione o seguinte conteúdo:
```
    <configuração>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">

    <!-- alterou o codificador -->
    <encoder class="com.newrelic.logging.logback.NewRelicEncoder"/>

  </appender>

  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
          <file>/tmp/spring-petclinic.log</file>
    <encoder class="com.newrelic.logging.logback.NewRelicEncoder"/>
  </appender>

  <root level="debug">
    <appender-ref ref="FILE" />
  </root>
</configuração>
```

Depois disso, precisamos reconstruir o aplicativo e executá-lo novamente:
```
    ./mvnw pacote -Dmaven.test.skip=true
    java -javaagent:./newrelic.jar -jar target/spring-petclinic-*-SNAPSHOT.jar
```

Então vamos visitar o aplicativo novamente para gerar mais tráfego, agora devemos ver as mensagens de log sendo relatadas.

    http://<VM_IP_ADDRESS>:8080
(sinta-se livre para navegar e clicar ao redor)

#### Nova configuração do log de infraestrutura da Relic
Precisamos configurar o agente de infraestrutura da New Relic para seguir o arquivo de log da Spring Pet Clinic e relatar os dados para o endpoint da New Relic.

Os arquivos de configuração de registro podem ser encontrados em /etc/newrelic-infra/logging.d/, com alguns exemplos

Então, precisamos criar um arquivo yaml

```
sudo vim /etc/newrelic-infra/logging.d/petclinic.yaml
```

e adicione as seguintes linhas:
```
logs:
   - nome: petclinic-log
     arquivo: /tmp/spring-petclinic.log
```

E cole o conteúdo do snippet acima.

Então acesse:
Explorar > Registros

(IMAGEM)

E você pode adicionar um filtro para selecionar apenas mensagens de log do seu host e do aplicativo Spring PetClinic:

Campos
**** hostname ****
**** servicename ****

Adicionar Filtro > Campos > service.name > nome do seu aplicativo

## Resumo
Este é o fim do exercício e certamente cobrimos muito terreno. Neste ponto, você deve ter métricas, traces e logs sendo reportados em sua conta New Relic.

A partir daqui podemos verificar e criar dashboards, alertas, etc.

(New Relic IO?)
