# Guia do usuário Heritrix
## Introdução:
Heritrix é o rastreador de web do Internet Archive com qualidade de arquivamento extensível, escálavel e de código aberto.
Esse guia explica como instalar , configurar e usar o Heritrix para rastrear a web. Assume-se que o usuário possui um conhecimento geral de conceitos computacionais como HTML e URLs.

### Usuários:
Os usuários desse documento constituem-se de administradores do Heritrix e outras equipes técnicas que desejam rastrear a internet usando o Heritrix.

### Versões:
As informações desse guia referem-se a versão 3.0 do Heritrix, a não ser que o contŕário seja informado. Seções referentes à versão 3.1 estão marcadas com a nota "A partir da versão 3.1".

## Requerimentos do sistema

## Atualizações das versões 3.0 e 3.1

Atualizações da versão 3.0:
1. Possibilidade de realizar vários rastreamentos simultaneos. O único limite de número de rastreamentos realizados simultaneamente refere-se a memória alocada no Heritrix.
2. Arquivo único XML de configuração baseado no framework Spring. Esse arquivo substitui o order.xml e outros arquivos de configuração do Heritrix 1.x.
3. Possibilidade de busca e modificação dos beans configurados por um navegador de uso simples. Olhar Bean Browser.
4. Extensabilidade aprimorada por meio do framework Spring. Por exemplo, substituições de domínios podem ser definidas em um nível mais refinado. Olhar Sheets.
5. Controle de uso mais seguro. HTTPS é usada para acessar e manipular o controle de uso do usuário.
6. Maior escabilidade.  Nas versões anteriores, rastreamentos com grandes valores de seed (dezenas ou centenas de milhões) podiam tentar utilizar memória além da alocada no Heritrix. Isso causava o interrompimento do rastreamento. A versão Heritrix 3.0 arrumou esses problemas, permitindo o processamento estável de rastreamentos de grande tamanho.
7. Flexibilidade maior ao modificar um rastreamento em andamento. Rastreamentos em andamento podem ser modificados pelo navegador Bean ou pelo Action Directory.
8. Introdução de filas paralelas (parallel queues). Ao rastrear sites específicos que aguentam tráficos altos, a opção das filas paralelas pode ser usada para abrir várias conexões de rastreamento concomitantes em um único site.
9. Controle de script que aceita input de scripts em vários formatos, como o AppleScript e o ECMAScript. Scripting pode ser usado para acessar e manipular, de forma programada, os componentes do núcleo do Heritrix.

Atualizações da versão 3.1 podem ser encontradas aqui.

## Instação do Heritrix
Uma distribuição binária do Heritrix pode ser baixada pelo link http://builds.archive.org:8080/maven2/org/archive/heritrix/heritrix/3.1.1/heritrix-3.1.1-dist.zip. Depois de baixado, expandir o arquivo. Esse processo funciona em algumas plataformas.

## Configuração do Heritrix

## Execução do Heritrix 3.0 e 3.1
O Heritrix pode ser usado a partir de diferentes linhas de comando. Insira o seguinte comando para visualizar as opções disponíveis. 
```
$HERITRIX_HOME/bin/heritrix --help
```
A tabela a seguir lista as opções de linhas de comando.

## Interface do usuário baseada na Web (IUW)
Depois de abrir o Heritrix, a interface do usuário baseada na web torna-se acessível.

A URI de acesso da Web IU normalmente é
https://(heritrixhost):8443

A IUW é protegida por senha. Não há nenhum login de acesso padrão. Um nome de usuário e uma senha devem ser fornecidos usando as opções de linha de comando -a ou -web-admin fornecidas no startup ou definindo a propriedade do sistema heritrix.cmdline.admin. O nome de usuário e a senha ficarão salvas (printed) no controle (console) no startup. A partir da versão 3.1, o nome de usuário do administrador e a senha não ficarão salvas no controle no startup. Também, a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador, adicionando mais uma medida de proteção ao nome de usuário e senha do administrador.

A página inicial de login solicita o nome de usuário e a senha. Depois de feito o login, a sessão será encerrada após um período sem uso.

O acesso da IUW é por HTTPS. O Heritrix é instalado com uma chave de acesso que contém um certificado auto-assinado. Isso fará com que o Mozila (navegador recomendado) exiba um prompt avisando que um certificaco auto-assinado está sendo usado. Siga as instruções abaixo para realizar o login no Heritrix pela primeira vez.

## Questões de segurança

O Heritrix é um aplicativo ativo e de grande rede que apresenta implicações de segurança tanto na máquina principal, onde ele roda, quanto remotamente, nas máquinas conectadas. 

### Entendendo os riscos
É importante entender que a IU da Web permite o acesso remoto do rastreador de maneiras que poderiam, potencialmente, interromper o rastreamento, mudar seu comportamento, ler ou criar arquivos de acesso local e fazer ou ativar outras ações no Java VM ou na máquina local pela execução de scripts arbitrários fornecidos pelo operador.

Um acesso não autorizado da IU da Web pode encerrar ou corromper um rastreamento. Também pode alterar o comportamento do rastreador fazendo com que ele seja um problema para os outros hosts da rede. Os arquivos acessíveis ao processo do rastreador podem ser excluídos, corrompidos ou substituídos, o que pode causar problemas extensos na máquina de rastreamento. 

Outro risco possível é que conteúdos worst-case ou mal-intencionados, em conjunto com os problemas do rastreador, podem interromper o rastreamento ou outros arquivos e operações no sistema local. Por exemplo, no passado, sem má intencão, alguns conteúdos de mídia avançada causaram o uso descontrolado de memória em bibliotecas de terceiros usadas pelo rastreador. Isso causou uma exaustão de memória que interrompeu e corrompeu o rastreamento em andamento. De forma parecida, padrões atípicos de inputs causaram  uso indevido da CPU pelas expressões regulares de extração de links do rastreador, causando rastreamentos extremamente lentos. Operadores de rastreamento devem monitorar seus rastreamentos de perto e usar a lista de discussão do projeto e o banco de dados de problemas para se manter atualizado sobre os problemas do rastreador.

### Controle de acesso à rede

Lançado sem nenhum bind-adress específico ('-b' flag), a IU da Web do rastreador vincula-se apenas ao endereço de localhost/loopback (127.0.0.1),  e, portanto, só é acessível pela rede na mesma máquina em que foi lançado.

Se prático, esta configuração padrão deve ser mantida. Uma técnica como o tunelamento SSH poderia ser usada por usuários autorizados da máquina de rastreamento para permitir o acesso à Web de sua máquina local à máquina de rastreamento. Por exemplo, imagine o Heritrix rodando numa máquina 'crawler.example.com', com sua IU da Web apenas ouvindo/ligado ao seu endereço de host local. Supondo que um usuário chamado 'crawloperator' tenha acesso SSH a 'crawler.example.com', ele pode emitir o seguinte comando SSH em sua máquina local:
```
ssh -L localhost:9999:localhost:8443 crawloperator@crawler.example.com -N
```
Isso diz ao SSH para abrir um túnel que encaminha conexões para "localhost: 9999" (na máquina local) para a própria ideia de "localhost: 8443" das máquinas remotas. Como resultado, a IU da Web do rastreador estará disponível por meio de "https: // localhost: 9999 /" enquanto o túnel existir (até que o comando ssh ou a conexão sejam interrompidos). Ninguém mais na rede pode conectar-se diretamente à porta 8443 em 'crawler.example.com' (já que está ouvindo apenas no endereço de loopback local), e ninguém em outro lugar na rede pode se conectar diretamente à porta 9999 do operador (já que também só está ouvindo o endereço de loopback local). 

Se você precisar da porta de escuta do Heritrix vinculada a um endereço público, o sinalizador de linha de comando '-b' poderá ser usado.  Esse sinalizador usa (como argumento) o nome do host/endereço a ser usado. O caractere '/' pode ser usado para indicar todos os endereços.

Se essa opção for usada, um conjunto de credenciais de login ainda mais unique/unguessable/brute-force-search-resistant devem ser escolhidas. Talvez ainda deva ser considerado o uso de outras políticas de rede/firewall para bloquear o acesso de origens não autorizadas.

### Controle de acesso de autenticação de login 

O usuário e a senha administrativos proporcionam uma segurança rudimentar contra acessos não autorizados. Para mais segurança, você deve: 
1. Usar um nome de usuário e senha únicos e difíceis de adivinhar para proteger a IU da Web. O Heritrix usa HTTPS para criptografar comunicações entre os clientes e a IU da Web. Tenha em mente que definir o nome de usuário e senha na linha de comando pode causar que eles fiquem visíveis para outros usuários da máquina de rastreamento - por exemplo, através da saída de uma ferramenta como 'ps' que mostra as linhas de comando usadas para processos de lançamento. Tenha em mente também que essas informações são ecoadas em texto simples no heritrix_out.log para referência do operador. A partir da versão 3.1, o nome de usuário e senha administrativos não são ecoados no heritrix_out.logl.  Ainda a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador. Assim, as credenciais não são visíveis para as outras máquinas que usam o comando listar processos (ps).
  2. Inicie a VM Java de hospedagem do Heritrix com uma conta de usuário que tenha os privilégios mínimos necessários para operar o rastreador. Isso limitará os danos no caso de a IU da Web ser acessada de maneira maliciosa.
  
## Um guia rápido para executar seu primeiro rastreamento

A página do Controle Principal aparece depois da instalação do Heritrix e do acesso da IUW.

1. Insira o nome da nova tarefa (job) na caixa de texto abaixo de "Criar nova tarefa com configuração inicial recomendada". Depois clique em "criar".

A tarefa recém criada aparecerá na lista de tarefas na página do Controle Principal. No Heritrix 3.0, a tarefa será baseada no perfil profile-defaults. A partir da versão 3.1, esse perfil foi eliminado. Ver Perfis para mais informações.

2. Clique no nome da nova tarefa e você será redirecionado para a página da tarefa.
O nome do arquivo de configuração, crawler-beans.cxml, aparecerá no topo da página. Ao lado, encontra-se a opção de "editar".

3. CLique em "editar" e os conteúdos do arquivo de configuração aparecerão em uma área de texto editável.

4. Nesse passo, várias propriedades devem ser inseridas para a tarefa tornar-se executável.
i. Primeiro, adicione um value válido na propriedade metadata.operatorContactUrl, como http://www.archive.org. 
ii. Em seguida, preencha o elemento `<prop>` do bean `longOverrides` com os valores do seed para o rastreamento.  Um seed de teste é configurado para referência. Quando terminar, clique em "salvar mudanças" no topo da página. Para mais informações sobre configuração de tarefas ver Configurando Tarefas e Perfis. 
  
5. Na página da tarefa, clique em "montar". Esse comando montará a infraestrutura necessária para executar a tarefa. A seguinte mensagem será exibida no registro de tarefas: "INFO JOB instanciado".

6. Em seguida, clique no botão "iniciar". Este comando inicia a tarefa no modo "pausado". Nesse passo, a tarefa está pronta para ser executada.

7. Para executar a tarefa, clique no botão de despausar. A tarefa, então, começará a enviar solicitações para as sementes do seu rastreamento. O status da tarefa será definido como "Em execução". Atualize a página para acompanhar as atualizações das estatísticas.

### Observação

* Uma tarefa não será modificada se o perfil ou a tarefa em que foi baseado for alterado.
* Tarefas baseadas no perfil padrão não estão prontas para serem executadas. O metadata.operatorContactUrl deve ser definido com um valor válido.

Mais informações sobre a avaliação do progresso de uma tarefa podem ser encontradas em Análise de Tarefas.

### Anexos

## Sair do Heritrix

Para sair do Heritrix, envie a combinação de teclas para o controle (console), o que forçará o processo a sair (como o Control-C). Para sair de um processo em segundo plano no qual o Heritrix está em execução, use o comando kill.

Para sair da versão 3.1, clique no botão "Sair do processo Java" depois de marcar "Tenho certeza".

## Editar uma tarefa em andamento

A configuração de uma tarefa pode ser editada enquanto ela ainda está em andamento. Isso pode ser feito pelo Bean Browser ou pelo link do Controle de Script na página da tarefa. O Bean Browser permite que as propriedades de tempo de execução sejam editadas.

O controle de script também pode ser usado para editar programaticamente tarefas em execução.

Se um valor não atômico for alterado, é recomendável pausar o rastreamento antes de fazer a alteração, pois algumas modificações nas entidades de configuração composta podem não ocorrer de maneira segura. Um exemplo de uma mudança não atômica é a adição de uma nova planilha.

A partir da versão 3.1, o tratamento de operações de orçamento de fila e de rotação/retirada foi refatorado para garantir que alterações feitas  durante o rastreamento (por meio de novas Sheets sobrepostas ou edição direta com a ferramenta Bean Browser ou controle de script) entrem em vigor imediatamente. O DispositionProcessor possui configuração de sobreposição de Sheets para aplicar essa marcação. Em geral, as alterações de configurações por meio de novas planilhas e associações de planilhas durante um rastreamento (conforme inserido via script) agora entram em vigor em todos os URIs que estão sendo retirados para processamento, em vez de apenas URIs recém-descobertos. Portanto, alterações feitas via bean-browse/scripting /new-sheet-overlays entram em vigor imediatamente.

## FTP

Heritrix suporta rastreamento de sites FTP. Seeds devem ser adicionados no seguinte formato: ftp://ftphostname.org/ftpdirectory.

## Ponto de verificação (Checkpoint)

O ponto de verificação de uma tarefa de rastreamento salva uma representação do estado atual da tarefa em um diretório no `checkpointsPath`, que recebe o nome do ponto de verificação. Essa representação inclui a serialização dos principais objetos da tarefa de rastreamento, cópias do conjunto atual de arquivos de log de bdbje e outros arquivos do estado do rastreamento.  O diretório do ponto de verificação contém tudo o que é necessário para recuperar um rastreamento. O ponto de verificação também roda os logs de rastreamento, incluindo o log `recover.gz`, se ativado. Arquivos Log não são copiados para o diretório do ponto de verificação e permanecem no diretógio de `logs`, sendo diferenciados através de sufixos. O sufixo é o nome do ponto de verificação; por exemplo, para o ponto `000031` o log do rastreamento será `crawl.log.000031`.

Para executar um ponto de verificação, clique no botão do ponto de verificação na página de tarefas da IUW ou invoque a funcionalidade do ponto de verificação pelo REST API. O ponto de verificação pode levar mais tempo em rastreamentos maiores (a partir da versão 3.1 esse processo é muito mais rápido). Enquanto o ponto de verificação estiver sendo feito, o status do rastreamento ficará como CHECKPOINTING. Após o término, o rastreador continuará o rastreamento, a não ser que ele estivesse pausado anteriormente. Nesse caso, o rastreamento continuará pausado.

A recuperação de um ponto de verificação é parecida com a recuperação de um rastreamento usando o `frontier.recovery.log`.

Ponto de verificação automático

Para configurar o Heritrix para executar pontos de verificação automaticamente, remova ou adicione a seguinte linha no arquivo `logging.properties`.

`org.archive.crawler.framework.Checkpointer.period=2`

Isso fará com que um Timer Thread seja instalado e executado em intervalos de hora. Veja `heritrix_out.log` para registrar informações sobre o Timer Thread.

A  partir da versão 3.1, 'hard links' (quando disponíveis) serão usados para coletar os arquivos BerkeleyDB-JE necessários para reproduzir o status do rastreamento. O deletamento automático de pontos de verificação antigos (arquivos ".DEL") foi reativado, eliminando a necessidade de excluir manualmente os arquivos desnecessários. Também facilita a movimentação e a limpeza de pontos de verificação antigos.

Recomeçar a partir de um ponto de verificação

A partir da versão 3.1, a IUW fornece a opção de recomeçar um rastreamento a partir de um ponto de verificação. Siga os passos abaixo para recomeçar um rastreamento a partir de um ponto de verificação:

1. Checkpoint o rastreamento em execução clicando no botão "checkpoint".
2. Quando o checkpoint terminar (uma mensagem aparecerá avisando o operador), encerre o rastreamento clicando no botão "terminar".
3. Desmonte a tarefa clicando no botão "desmontar". 
4. Reconstrua a tarefa clicando no botão "construir". Agora, uma caixa suspensa deve aparecer em cima dos botões de comando. A caixa suspensa tem os nomes dos pontos de verificação invocados anteriormente.
5. Selecione um checkpoint da caixa. O checkpoint selecionado será usado para iniciar a tarefa recém-construída.
6. Clique em "iniciar".
7. Clique em "despausar".
A tarefa iniciará a partir do ponto de verificação escolhido.

## Página do Controle Principal

A página do controle principal aparece logo após o login. Esse página lista todas as tarefas e perfis.

### Elementos e operações de dados do controle principal

reexaminar

O botão de "reexaminar" faz com que o Heritrix examine o sistema de arquivos procurando qualquer mudança no diretório "tarefas". A exibição é, então, sincronizada com o sistema de arquivos

criar

O botão "criar" permite que um nome seja inserido e uma nova tarefa de rastreamento seja criada. A tarefa de rastreamento será baseada nos perfis padrões.

adicionar

O botão "adicinar" permite que um diretório de tarefa não gerenciado pelo Heritrix seja especificado. Após inserir o caminho para o novo diretório e clicar "adicionar", o Heritrix permitirá que você administre o diretório. Por exemplo, será possível configurar a tarefa usando o arquivo crawler-beans.cxml.

status

O status das tarefas em andamento, o número de vezes que uma tarefa foi iniciada e o caminho para o arquivo de configuração das tarefas aparecem na página do Controle Principal. Também aparecem as estatísticas de memória do Heritrix e se a tarefa é um perfil ou não.

Encerrar o Processo Java

A partir da versão 3.1, existe o botão "Encerrar o Processo Java". Após ser selecionado juntamente com a seleção da opção "Tenho certeza", esse botão fará com que o Heritrix seja encerrado e fechado.

## Beans Browser

A IUW fornece uma maneira de visualizar e editar os beans Spring que formam a configuração de um rastreamento. É importante notar que mudar a configuração de um rastreamento usando o Bean Browser não fará com que o arquivo crawler-beans.cxml seja atualizado. Portanto, mudanças de definições feitas com o Beans Browser não são permanente. O Bean Browser deve ser usado apenas para mudar as definições de um rastreamento em andamento.  Para acessar o navegador, clique no link do Bean Browser na página de tarefas. A hierarquia dos beans Spring aparecerá.

Os beans individuais podem ser detalhados ao clilcar neles. O exemplo abaixo mostra a exibição depois de clicar nos seeds beans. 

## Perfis

Perfis são modelos para a criação de tarefas de rastreamento. Contêm todas as configurações de uma tarefa de rastreamento, mas não são considerados rastreáveis. O Heritrix não permite que um perfil seja diretamente rastreado, apenas tarefas baseadas em perfis.

Um exemplo comum de uma configuração de perfil é deixar a propriedade `metadata.operatorContactUrl` indefinida, forçando o operador a inserir um valor válido.

Os perfis podem ser usados como modelos ao deixar suas definições de configuração inválidas. Dessa meneira, o operador é obrigado a escolher suas configurações ao criar uma tarefa a partir de um perfil. Isso pode ser vantajoso quando um administrador precisar configurar várias tarefas de rastreamento diferentes para acomodar sua política de rastreamento.

Se uma tarefa de rastreamento é um perfil ou uma tarefa que pode ser iniciada é determinado por um nome de arquivo do arquivo de configuração principal. Se começar com "profile-", é um perfil. Tenha cuidado ao alterar o nome de um arquivo de configuração principal ao copiar manualmente o perfil para criar uma tarefa de rastreamento possível de incialização.

A partir da versã 3.1, a marca "profile-" foi eliminada. Não há mais restrições em nomes de perfis.

## Heritrix Output

Além dos logs, os seguintes arquivos são gerados. Algumas das informações contidas neles também estão presentes na IUW.

* surts.dump
* negative-surts.dump
* heritrix_out.log
* crawl-report.txt
* hosts-report.txt
* mimetype-report.txt
* processors-report.txt
* responsecode-report.txt
* seeds-report.txt
* frontier-summary-report.txt
* source-report.txt
* threads-report.txt
* arquivos WARC

surts.dump

Contém os formulários ﻿SURTs das URIs seed.

negative-surts.dump

Contém o formulário SUR de URIs que devem ser excluídos do rastreamento.

heritrix_out.log
This file captures output to standard out and standard error.  Most of the output consists of low-level exceptions and logging information.

Esse arquivo é criado no mesmo diretório do arquivo jar do Heritrix. Não é associado com nenhuma tarefa específica, mas contém outputs de todos as tarefas executadas pelo rastreador.

Exemplo de uma saída desse arquivo:

```
Darwin internet-archives-macbook-pro.local 10.0.0 Darwin Kernel Version 10.0.0: Fri Jul 31 22:47:34 PDT 2009; root:xnu-1456.1.25~1/RELEASE_I386 i386
java version "1.6.0_15"
Java(TM) SE Runtime Environment (build 1.6.0_15-b03-219)
Java HotSpot(TM) 64-Bit Server VM (build 14.1-b02-90, mixed mode)
JAVA_OPTS= -Xmx256m
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
file size               (blocks, -f) unlimited
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 256
pipe size            (512 bytes, -p) 1
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 266
virtual memory          (kbytes, -v) unlimited
```

crawl-report.txt

Contém métricas úteis sobre tarefas terminadas. O relatório é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento.  

Exemplo de uma saída desse arquivo:

```
Crawl Name: basic
Crawl Status: Finished
Duration Time: 1h33m38s651ms
Total Seeds Crawled: 1
Total Seeds not Crawled: 0
Total Hosts Crawled: 1
Total URIs Processed: 1337
URIs Crawled successfully: 1337
URIs Failed to Crawl: 0
URIs Disregarded: 0
Processed docs/sec: 0.24
Bandwidth in Kbytes/sec: 4
Total Raw Data Size in Bytes: 23865329 (23 MB)
Novel Bytes: 23877375 (23 MB)
```

hosts-report.txt

Contém uma visão geral dos hosts que foram rastreadas. Também mostra o número de documentos rastreados e a quantidade de bytes baixados por cada host.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse arquivo:

```
1337 23877316 www.smokebox.net 0 0
1 59 dns: 0 0
0 0 dns: 0 0
```

mimetype-report.txt

Contém um relatório mostrando o número de documentos e dados baixados per mime type is displayed.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse arquivo:

```
624 13248443 image/jpeg
450 8385573 text/html
261 2160104 image/gif
1 74708 application/x-javascript
1 59 text/dns
1 8488 text/plain
processors-report.txt
```

Contém o relatório dos processadores, que mostra a atividade de cada processador do Heritrix. Para mais informações, ver Processing Chains.  É escrito no fim do rastreamento. 

Exemplo de uma saída desse arquivo:

```
CandidateChain - Processors report - 200910300032
  Number of Processors: 2

Processor: org.archive.crawler.prefetch.CandidateScoper

Processor: org.archive.crawler.prefetch.FrontierPreparer


FetchChain - Processors report - 200910300032
  Number of Processors: 9

Processor: org.archive.crawler.prefetch.Preselector

Processor: org.archive.crawler.prefetch.PreconditionEnforcer

Processor: org.archive.modules.fetcher.FetchDNS

Processor: org.archive.modules.fetcher.FetchHTTP
  Function:          Fetch HTTP URIs
  CrawlURIs handled: 1337
  Recovery retries:   0

Processor: org.archive.modules.extractor.ExtractorHTTP
  Function:          Extracts URIs from HTTP response headers
  CrawlURIs handled: 1337  Links extracted:   0

Processor: org.archive.modules.extractor.ExtractorHTML
  Function:          Link extraction on HTML documents
  CrawlURIs handled: 449
  Links extracted:   6894

Processor: org.archive.modules.extractor.ExtractorCSS
  Function:          Link extraction on Cascading Style Sheets (.css)
  ExtractorURIs handled: 0
  Links extracted:   0

Processor: org.archive.modules.extractor.ExtractorJS
  Function:          Link extraction on JavaScript code
  CrawlURIs handled: 1
  Links extracted:   19

Processor: org.archive.modules.extractor.ExtractorSWF
  Function:          Link extraction on Shockwave Flash documents (.swf)
  CrawlURIs handled: 0
  Links extracted:   0


DispositionChain - Processors report - 200910300032
  Number of Processors: 3

Processor: org.archive.modules.writer.WARCWriterProcessor

Processor: org.archive.crawler.postprocessor.CandidatesProcessor

Processor: org.archive.crawler.postprocessor.DispositionProcessor
```

responsecode-report.txt

Contém um relatório que exibe o número de documentos baixados per successful status code.  For failure codes see the crawl.log file.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse relatório:

```
[#urls] [rescode]
1306 200
31 404
1 1
```

seeds-report.txt

Contém o status de rastreamento de cada seed.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse relatório:

```
[code] [status] [seed] [redirect]
200 CRAWLED http://www.smokebox.net
```

frontier-summary-report.txt

Este relatório contém um detalhamento da atividade de fronteira por thread. Para cada encadeamento em execução, o status da fila de fronteira pode ser examinado.

Exemplo de uma saída desse relatório:

```
-----===== RETIRED QUEUES =====-----
internet-archives-macbook-pro:reports hstern$
internet-archives-macbook-pro:reports hstern$ cat frontier-summary-report.txt
Frontier report - 201001142216
 Job being crawled: basic

 -----===== STATS =====-----
 Discovered:    3
 Queued:        0
 Finished:      3
  Successfully: 2
  Failed:       0
  Disregarded:  1

 -----===== QUEUES =====-----
 Already included size:     3
               pending:     0

 All class queues map size: 1
             Active queues: 1
                    In-process: 0
                         Ready: 1
                       Snoozed: 0
           Inactive queues: 0 (p1: 0)
            Retired queues: 0
          Exhausted queues: 0

 -----===== MANAGER THREAD =====-----
Java Thread State: RUNNABLE
Blocked/Waiting On: NONE
    java.lang.Thread.getStackTrace(Thread.java:1460)
    org.archive.crawler.framework.ToeThread.reportThread(ToeThread.java:476)
    org.archive.crawler.frontier.WorkQueueFrontier.standardReportTo(WorkQueueFrontier.java:1424)
    org.archive.crawler.frontier.WorkQueueFrontier.reportTo(WorkQueueFrontier.java:1257)
    org.archive.crawler.reporting.FrontierSummaryReport.write(FrontierSummaryReport.java:41)
    org.archive.crawler.reporting.StatisticsTracker.writeReportFile(StatisticsTracker.java:973)
    org.archive.crawler.reporting.StatisticsTracker.dumpReports(StatisticsTracker.java:1000)
    org.archive.crawler.reporting.StatisticsTracker.crawlEnded(StatisticsTracker.java:572)
    org.archive.crawler.reporting.StatisticsTracker.onApplicationEvent(StatisticsTracker.java:1044)
    org.springframework.context.event.SimpleApplicationEventMulticaster$1.run(SimpleApplicationEventMulticaster.java:78)
    org.springframework.core.task.SyncTaskExecutor.execute(SyncTaskExecutor.java:49)
    org.springframework.context.event.SimpleApplicationEventMulticaster.multicastEvent(SimpleApplicationEventMulticaster.java:76)
    org.springframework.context.support.AbstractApplicationContext.publishEvent(AbstractApplicationContext.java:274)
    org.archive.crawler.framework.CrawlController.sendCrawlStateChangeEvent(CrawlController.java:323)
    org.archive.crawler.framework.CrawlController.completeStop(CrawlController.java:375)
    org.archive.crawler.framework.CrawlController.noteFrontierState(CrawlController.java:639)
    org.archive.crawler.frontier.AbstractFrontier.reachedState(AbstractFrontier.java:484)
    org.archive.crawler.frontier.AbstractFrontier.managementTasks(AbstractFrontier.java:412)
    org.archive.crawler.frontier.AbstractFrontier$2.run(AbstractFrontier.java:328)

 -----===== LONGEST QUEUE =====-----
Queue com,fizzandpop,www, (p1)
  0 items
    last enqueued: http://www.fizzandpop.com/
      last peeked: http://www.fizzandpop.com/
   total expended: 2 (total budget: -1)
   active balance: 2998
   last(avg) cost: 1(1)
   totalScheduled fetchSuccesses fetchFailures fetchDisregards fetchResponses robotsDenials successBytes totalBytes fetchNonResponses
   3 2 0 1 2 1 383 383 2
   SimplePrecedenceProvider
   1


 -----===== IN-PROCESS QUEUES =====-----

 -----===== READY QUEUES =====-----
READY#0:
Queue com,fizzandpop,www, (p1)
  0 items
    last enqueued: http://www.fizzandpop.com/
      last peeked: http://www.fizzandpop.com/
   total expended: 2 (total budget: -1)
   active balance: 2998
   last(avg) cost: 1(1)
   totalScheduled fetchSuccesses fetchFailures fetchDisregards fetchResponses robotsDenials successBytes totalBytes fetchNonResponses
   3 2 0 1 2 1 383 383 2
   SimplePrecedenceProvider
   1


 -----===== SNOOZED QUEUES =====-----

 -----===== INACTIVE QUEUES =====-----

 -----===== RETIRED QUEUES =====-----
 ```
 
source-report.txt

Contém uma linha de item para cada host, que inclui o seed por qual o host foi alcançado.

Exemplo de uma saída desse relatório:

```
[source] [host] [#urls]
http://www.fizzandpop.com/ dns: 1
http://www.fizzandpop.com/ www.fizzandpop.com 1
```

Note

The sourceTagSeeds property of the TextSeedModule bean must be set to true for this report to be generated.
<bean id="seeds" class="org.archive.modules.seeds.TextSeedModule">

<property name="textSource">

<bean class="org.archive.spring.ConfigString">

<property name="value">
<value>
# [see override above]
        </value>
</property>
</bean>
</property>
<property name="sourceTagSeeds" value="true"/>
</bean>
threads-report.txt
This report contains the list of threads that were active at the end of the crawl.  Detailed information about each thread is also available.

WARC files
Assuming you are using the WARC writer that comes with Heritrix, a number of WARC files will be generated containing crawled content.

You can specify the storage location of WARC files by setting the directory value of the WARCWriterProcessor bean.

WARC files are named using the following convention:

[prefix][12 digit timestamp][series padded to 5 digits][crawler hostname].warc.gz

The WARCWriterProcessor contains the prefix setting.  By default it is IAH.

WARC files with an .open suffix are in the process of being written to by Heritrix.  There may be multiple open WARCs at any given time.

WARC files with an .invalid suffix indicate problems writing to the file.  This may be the result of a bad disk or a fully utilized disk.  On an I/O problem, Heritrix closes the problematic WARC file and gives it an .invalid suffix.  These files should be checked for coherence.

As of Heritrix 3.1, the "LowDiskPauseProcessor" bean has been replaced by the "DiskSpaceMonitor" bean.  When writing WARC files, the DiskSpaceMonitor checks the available space on the configured paths and if free space has dropped below the defined threshold the crawl is paused.  In the example below, the path /warcs is monitored.  If the level of free space drops below 500MB the crawls writing to the /warcs directory are paused.

<bean id="diskSpaceMonitor" class="org.archive.crawler.monitor.DiskSpaceMonitor"> <property name="pauseThresholdMiB" value="500" /> <property name="monitorConfigPaths" value="true" /> <property name="monitorPaths"> <list> <value>/warcs</value> </list> </property> </bean>
As of Heritrix 3.1, the naming convention for WARC files has changed.  Instead of specifying the formula for ARC/WARC naming in code and using a supplied 'prefix' and 'suffix', a template with variable interpolation may be used.  The configured 'prefix' remains an available variable, as well as other useful local machine, crawl, and writer properties. The default template is:

${prefix}-${timestamp17}-${serialno}-${heritrix.pid}~${heritrix.hostname}~${heritrix.port}
The template adds the local process ID and the 17 digit timestamp.  The timestamp is provided by a service that ensures each timestamp is at least 1 millisecond after previous millisecond values.  The new default convention also minimizes the likelihood of ARC/WARC name collisions, even when many crawls are launched or running simultaneously on the same local machine, using the same file name prefix.  Although the generated names are long, they are very likley to be unique under normal conditions.  It is not recommended that the template by changed unless the alternate naming system is certain to also generate unique names.  This is important because down stream tools that index ARCs/WARCs often assume file name uniqueness and can benefit from their unique generation.
