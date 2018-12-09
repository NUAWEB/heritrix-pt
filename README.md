# Guia de usuário Heritrix
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
1. Usar um nome de usuário e senha únicos e difíceis de adivinhar para proteger a IU da Web. O Heritrix usa HTTPS para criptografar comunicações entre os clientes e a IU da Web. Tenha em mente que definir o nome de usuário e senha na linha de comando pode causar que eles fiquem visíveis para outros usuários da máquina de rastreamento - por exemplo, através do output de uma ferramenta como 'ps' que mostra as linhas de comando usadas para processos de lançamento. Tenha em mente também que essas informações são ecoadas em texto simples no heritrix_out.log para referência do operador. A partir da versão 3.1, o nome de usuário e senha administrativos não são ecoados no heritrix_out.logl.  Ainda a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador. Assim, as credenciais não são visíveis para as outras máquinas que usam o comando listar processos (ps).
  2. Inicie a VM Java de hospedagem do Heritrix com uma conta de usuário que tenha os privilégios mínimos necessários para operar o rastreador. Isso limitará os danos no caso de a IU da Web ser acessada de maneira maliciosa.
  
## Guia rápido para executar seu primeiro rastreamento

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

Exemplo de um output desse arquivo:

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

Exemplo de um output desse arquivo:

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

Exemplo de um output desse arquivo:

```
1337 23877316 www.smokebox.net 0 0
1 59 dns: 0 0
0 0 dns: 0 0
```

mimetype-report.txt

Contém um relatório mostrando o número de documentos e dados baixados per mime type is displayed.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de um output desse arquivo:

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

Exemplo de um output desse arquivo:

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

Exemplo de um output desse relatório:

```
[#urls] [rescode]
1306 200
31 404
1 1
```

seeds-report.txt

Contém o status de rastreamento de cada seed.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de um output desse relatório:

```
[code] [status] [seed] [redirect]
200 CRAWLED http://www.smokebox.net
```

frontier-summary-report.txt

Este relatório contém um detalhamento da atividade de fronteira por thread. Para cada encadeamento em execução, o status da fila de fronteira pode ser examinado.

Exemplo de um output desse relatório:

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

Exemplo de uma 
desse relatório:

```
[source] [host] [#urls]
http://www.fizzandpop.com/ dns: 1
http://www.fizzandpop.com/ www.fizzandpop.com 1
```

Observação

* A propriedade `sourceTagSeeds` do bean `TextSeedModule` deve ser definida como "true" para gerar esse relatório.

```
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
```

threads-report.txt

Contém a lista de encadeamentos ainda ativos no final do rastreamento, com informações detalhadas sobre cada um.

arquivos WARC 

Assuming you are using the WARC writer that comes with Heritrix, vários arquivos WARC contendo conteúdo rastreado serão gerados.

O local de armazenamento de arquivos WARC pode ser especificado definindo o valor do `diretório` do bean `WARCWriterProcessor`.

Arquivos WARC são nomeados conforme a seguinte convenção:

[prefix][12 digit timestamp][series padded to 5 digits][crawler hostname].warc.gz

O WARCWriterProcessor contém a configuração de prefixo padrão IAH.

Arquivos WARC com o sufixo `.open` estão no processo de serem escritos pelo Heritrix. Pode haver vários WARCs abertos a qualquer momento.

Arquivos WARC com o sufixo `.invalid` indicam problemas ao gravar no arquivo. Isso pode ser causado por um disco danificado ou um disco sem espaço. Em um problema de I/O, o Heritrix fecha o arquivo WARC problemático e adiciona o sufixo `.invalid`. Esses arquivos devem ser verificados quanto à coerência.

A partir da versão 3.1, o bean "LowDiskPauseProcessor" foi substituido pelo bean "DiskSpaceMonitor". Ao gravar arquivos WARC, o DiskSpaceMonitor verifica o espaço disponível dos caminhos configurados. Se o espaço livre estiver abaixo do limite definido, o rastreamento será pausado. No exemplo abaixo, o caminho `/warcs` é monitorado. Se o espaço livre for inferior a 500MB, os rastreamentos sendo gravados no diretório `/warcs` serão pausado.
```
<bean id="diskSpaceMonitor" class="org.archive.crawler.monitor.DiskSpaceMonitor"> <property name="pauseThresholdMiB" value="500" /> <property name="monitorConfigPaths" value="true" /> <property name="monitorPaths"> <list> <value>/warcs</value> </list> </property> </bean>
```

A partir da versão 3.1, a nomenclatura de arquivos WARC foi alterada. Em vez de especificar a fórmula para a nomenclatura ARC/WARC no código e usar um 'prefixo' e 'sufixo' fornecidos, um modelo com interpolação variável pode ser usado.  O "prefixo" configurado continua disponível como uma variável, assim como outras propriedades úteis de máquina, rastreamento e gravações. O modelo padrão é:

```
${prefix}-${timestamp17}-${serialno}-${heritrix.pid}~${heritrix.hostname}~${heritrix.port}
```

O modelo adiciona o processo de ID local e a data e hora de 17 digitos. O registro de data e hora é fornecido por um serviço que assegura que cada registro seja pelo menos 1 milissegundo após os valores anteriores em milissegundos. A nova convenção padrão também minimiza as chances de colisões de nomes ARC/WARD, mesmo quando vários rastreamentos estão sendo iniciados ou executados simultaneamente na mesma máquina local, usando o mesmo prexifo de nome de arquivo. Apesar dos nomes gerados serem longos, eles provavelmente serão únicos sob condiçoes normais. Não é recomendado que o modelo seja alterado, a menos que seja certo que a nomenclatura alternativa fornecerá nomes únicos. Isso é importante porque as ferramentas downstream que indexam ARCs/WARCs geralmente pressupõem a exclusividade do nome do arquivo e podem se beneficiar de sua geração exclusiva.

## Casos comuns de uso do Heritrix

Existem várias maneiras de executar um rastreamento da web. Aqui listamos vários casos que ajudam na familiarização com alguns dos parâmetros de rastreamento mais usados do Heritrix.

### Arquivando conteúdo de mídia avançada

Essa seção do guia de usuário Heritrix examina os desafios que arquivistas de conteúdo de mídia avançada enfrentam e oferece sugestões específicas para optimizar o rastreador do Heritrix, fazendo com que ele lide com esses desafios.

Mídia avançada abrange diferentes tipos de conteúdo da web que proporcionam aos usuários uma experiência superior a de páginas de text/html normais, entre eles: vídeos, animações, imagens e áudios. Tecnologias interativas que vão além da entrada básica de dados FORM também são um tipo de mídia avançada: wikis, como a Wikipedia; tecnologias que fornecem aos usuários controle refinado sobre conteúdo da Web, como AJAX; tecnologia Flash, que expõe uma ampla variedade de widgets de interface de usuário que permitem arranjos espaciais de dados muito além dos recursos da primeira geração de Websites. Além disso, é caracterizada por arquivos de tamanho maior que páginas de `text/html` normais.

* Arquivos de grande tamanho

Conteúdos de mídia avançada, como o Flash e vídeos, normalmente são muito maiores do que páginas `text/html` normais. O rastreamento desse tipo de conteúdo requer grandes investimentos em armazenamento e largura de banda (bandwidht). Para diminuir esses problemas, é recomendada a desduplicação para rastreamentos desse tipo de conteúdo. A desduplicação identifica conteúdos coletados anteriormente que são redundantes e pula seus downloads. Pointers em conteúdos duplicados permitem que eles apareçam em rastreamentos subsequentes. Para mais detalhes ver Configurando o Heritrix para Desduplicação.

* Links incorporados em mídia avançada

Várias tecnologias de mídia avançada permitem que links sejam incorporados em formatos de arquivos que não são propícios à extração de links. Ao rastrear um site de conteúdo de mídia avançada, é importante identificar se há um site-map no site. Um site-map é uma página HTML que contém links de todas as páginas importantes do site. Ao adicionar a URI do site-map como uma seed para um rastreamento de mídia avançada, links que, caso contrário, não seriam extraídos, serão arquivados.

* Uso excessivo de memória e CPU

O download de conteúdo de mídia avançada pode causar uma carga excessiva na memória e na CPU dos computadores de rastreamento. Por exemplo, a extração de links do Flash e de outros recursos de mídia avançada exige análise extensiva de dados, o que exige muito da CPU.
Padrões de entrada atípicos também podem causar uso excessivo da CPU quando expressões regulares usadas pelo Heritrix são executadas. Portanto, é recomendado que os rastreamentos de mídia avançada recebam mais memória e CPU do que os rastreamentos "normais". A memória alocada ao Heritrix é definida a partir da linha de comando. O exemplo abaixo mostra a opção de linha de comando usada para alocar 1GB de memória ao Heritrix, o que deve ser suficiente para a maioria dos rastreamentos de mídia avançada:

```
export JAVA_OPTS=-Xmx1024M
```

Processadores multi-core também são recomendados para rastreamentos de mídia avançada.

* Mídia de transmissão (streaming media)

Streaming media is media content delivered sequentially over time to a media-consumer from a media-producer.  Examples of streaming media include Internet Radio and TV.  Streaming media is concerned with the delivery mechanism of the media format and not the format itself.   Heritrix can capture media streamed over HTTP or FTP, but does not recognize other streaming protocols such as Real Time Streaming Protocol (RTSP).  This limitation has generated interest in embedding a media player in Heritrix that does recognize most streaming formats.  For more information on embedding a Media Player in Heritrix, see the "Archiving Streaming Media on the Web Proof of Concept and First Results" article in the International Web Archiving Workshop 2006 conference paper at http://iwaw.europarchive.org/06/PDF/iwaw06-proceedings.pdf.

* Redes sociais

Vários sites de redes sociais utilizam mídia avançada para melhorar a experiência dos ususários. Para diretrizes específicas sobre como arquivar sites de redes sociais, ver Archiving Social Networking Sites with Archive-It. Essas instruções se aplicam ao aplicativo Archive-It, criado a partir do Heritrix.

### Como evitar solicitações falsas ao processar certos tipos de conteúdos

A partir da versão 3.1, melhorias foram feitas na capacidade do rastreador de determinar se uma string é um URI válido. Essas melhorias proporcionam uma melhor extração de links de conteúdos como o JavaScript não analisado/não interpretado. No entanto, essa técnica pode ser propensa a erros, causando problemas ou incomodações no website alvo. Na versão 3.1, essa funcionalidade pode ser desativada para rastreamentos completos ou site-por-site. Para desativar, é necessário remover a referência bean "extractorJs" do bean "fetchProcessors" e definir as propriedades "extractJavascript" e "extractValueAttributes" do "extractionHtml" como falsas.

1. Remova a referência do bean "fetchProcessors" para "extractorJs".

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<!-- ****** <ref bean="extractorJs"/> ****** -->
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>

```

2. Defina as configurações "extractJS" e "extractValueAttributes" do bean "extractHtml" como falsas (false).

```

<bean id="extractorHtml" class="org.archive.modules.extractor.ExtractorHTML">
<property name="extractJavascript" value="false" />
<property name="extractValueAttributes" value="false" />
</bean>

```

### Como evitar grande números de conteúdos dinâmicos

Suponha que você queira apenas rastrear páginas de um determinado host (http://www.foo.org/) e também evitar o rastreamento de muitas páginas de um calendário gerado dinamicamente. Vamos supor que o calendário é acessado ao fornecer um ano, um mês e um dia para o diretório do calendário. Por exemplo, .

Quando você cria uma tarefa de rastreamento você especifica apenas um seed: http://www.foo.org/.  Por padrão, a nova tarefa de rastreamento usará a DecideRuleSequence, que irá conter um conjunto padrão de DecideRules. Uma das regras padrão é a SurtPrefixedDecideRule, que informa ao Heritrix para aceitar qualquer URI que corresponda ao prefixo SURT do URI de origem: http://(org,foo,www,)/. Se a URI http://foo.org/ for encontrada, será rejeitada, já que seu prefixo SURT http://(org,foo,) não corresponte ao prefixo SURT do seed.  Para permitir que ambos foo.org e www.foo.org sejam capturados, você pode adicionar dois seeds: http://www.foo.org/ and http://foo.org/. Para permitir que todo subdomínio de foo.org seja rastreado, você pode adicionar a semente http://foo.org. Observe a ausência de uma barra à direita.

Apague o TranclusionDecideRule, pois essa regra tem o potencial de levar o Heritrix para outro host. Por exemplo, se um URI retornar um código de resposta 301 (mover permanentemente) ou 302 (encontrado), bem como um URI que contenha um nome de host diferente do que os seeds, o Heritrix aceitaria esse URI usando o TransclusionDecideRule. Remover essa regra fará com que o Heritrix não se afaste do nosso host www.foo.org.

PathologicalPathDecideRule e TooManyPathSegmentsDecideRule permitirão que o Heritrix evite alguns tipos de armadilhas de rastreamento. TooManyHopsDecideRule fará com que o Heritrix não se distancie muito do seed. Dessa forma, o Heritrix não será preso num loop infinito pelo calendário.  Por padrão, o máximo de saltos é definido como 20, mas esse valor pode ser alterado editando o arquivo crawler-beans.cxml.

Como alternativa, você pode adicionar o MatchesFilePatternDecideRule. Defina usePresetPattern como CUSTOM e defina o regexp como: .foo \ .org (?! / Calendar). |. * Foo \ .org / calendar? Year = 200 [56]. *

### Espelhamento de arquivos HTML

Suponha que você queira apenas rastrear URIs que correspondam a http://foo.org/bar/\*.html e salvar os arquivos rastreados em um formato de arquivo/diretório em vez de arquivos WARC. Além disso, suponha que o servidor da Web faz distinção entre maiúsculas e minúsculas.  Por exemplo, http://foo.org/bar/abc.html e http://foo.org/bar/ABC.HTML estão apontando para dois recursos diferentes.

Primeiro, crie uma tarefa com apenas um see, http://foo.org/bar/. Configure o bean warcWriter para que sua classe seja org.archive.modules.writer.MirrorWriterProcessor. Esse Processador armazenará arquivos em uma estrutura de diretórios que corresponda aos URIs rastreadas. Os arquivos serão armanezados na cópia do diretório da tarefa de rastreamento.  The files will be stored in the crawl job's mirror directory.

### Armazenamento de páginas HTML bem-sucedidas

Suponha que você queira capturar as primeiras 50 páginas encontradas de um conjunto de seeds e arquivar apenas as páginas que retornam um código de resposta 200 e text/html de tipo mime.  Além disso, você quer procurar links somente em recursos HTML.

Para verificar links apenas em documentos HTML, você precisará remover os seguintes extratores que informam ao Heritrix para procurar links em folhas de estilo, JavaScript e arquivos Flash:

* ExtractorCss
* ExtractorJs
* ExtractorSwf

Mantenha o ExtractorHttp, já que é útil para localizar recursos que só podem ser encontrados usando um redirecionamento (código de resposta 301 ou 302).

O número de URIs baixados pode ser limitado definindo a propriedade maxDocumentsDownload no bean crawlLimiter. Configurar o valor para 50 provavelmente não fornecerá os resultados pretendidos. Já que cada resposta DNS e arquivo robots.txt são contados no número, o valor deve ser definido para 50 * o número de seeds * 2.

## Tarefas (jobs)

Uma tarefa (às vezes chamada de tarefa de rastreamento) é uma configuração que define a forma como um rastreamento é executado. Uma tarefa contém informações como os seeds a serem rastreados, o endereço de e-mail do operador e outras informações de configuração que determinam como os dados de seed são acessados e armazenados. Um rastreamento é uma isntância executada de uma tarefa.

No Heritrix, uma tarefa é baseada no Framework Spring. Beans Spring representam os atributos de uma tarefa. O snippet abaixo mostra um bean Spring que faz parte de uma configuração de tarefa (os beans Spring são definidos no formato XML). O bean tem uma propriedade que é configurada para os URIs dos seeds a serem rastreados. 

```
<bean id="longerOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">
<property name="properties">
<props>
      <prop key="seeds.textSource.value"># URLS HERE http://example.example/example</prop>
</props>
</property>
</bean>
```

## Análise da tarefa

O Heritrix oferece várias facilidades para examinar os detalhes de um rastreamento. Esses relatórios e logs também estão disponíveis durante o tempo de execução.

### Logs

Cada tarefa de rastreamento tem seu próprio conjunto de arquivos de logs.

Os logs podem sem encontrados no diretório "logs", que existe sob o diretório de uma tarefa específica. A localização de arquivos logs específicos é fornecida na seção "Caminhos referenciados à configuração" da página da tarefa.

Propriedades logs

As propriedades de log podem ser configuradas modificando o arquivo logging.properties localizado no diretório ./conf. Para obter informações sobre como usar propriedades de log, visite http://logging.apache.org/log4j/.

Arquivos log

alerts.log

Esse log contém alertas que indicam problemas com os rastreamentos.

crawl.log

Cada URI que o Heritrix tenta buscar fará com que uma linha de log seja gravada no arquivo crawl.log. Abaixo está um extrato de duas linhas do log.

```
2011-06-23T17:12:08.802Z   200       1299 http://content-5.powells.com/robots.txt LREP http://content-5.powells.com/cgi-bin/imageDB.cgi?isbn=9780385518635 text/plain #014 20110623171208574+225 sha1:YI
UOKDGOLGI5JYHDTXRFFQ5FF4N2EJRV - -
2011-06-23T17:12:09.591Z   200      15829 http://www.identitytheory.com/etexts/poetics.html L http://www.identitytheory.com/ text/html #025 20110623171208546+922 sha1:7AJUMSDTOMT4FN7MBFGGNJU3Z56MLCMW
- -
```

| Nome do campo  | Descrição |
| ------------- | ------------- |
| Timestamp  | The timestamp in ISO8601 format, to millisecond resolution.  The time is the instant of logging.  |
| Fetch Status Code  | Usually this is the HTTP response code but it can also be a negative number if URI processing was unexpectedly terminated.  |
| Tamanho do documento | Tamanho do documento baixado, em bytes. Para HTTP, é apenas o tamanho do conteúdo. O tamanho exclui os cabeçalhos de resposta HTTP. Para DNS, o campo de tamanho é o tamanho total da resposta do DNS. |
| URI baixado | O URI do documento baixado. |
| Caminho de Descoberta | Códigos de navegação (caminho de descoberta) que mostram a trilha de downloads que levam ao URI baixado. A partir da versão 3.1, o comprimento do caminho de descoberta foi limitado aos últimos 50 hop-types.  Por exemplo, um caminho de 62-hop pode aparecer, a partir de agora, como "12+LLRLLLRELLLLRLLLRELLLLRLLLRELLLLRLLLRELLLLRLLLRELE".  Esse aprimoramento diminui o tamanho do log e limita o uso de memória. Os códigos de navegação são os seguintes. |
| Referenciador | O URI que precedeu imediatamente o URI baixado. Este é o referenciador. O caminho de descoberta e o referenciador estarão vazios para URIs de seeds. |
| Mime Type | The downloaded document mime type. |
| Worker Thread ID | The id of the worker thread that downloaded the document. |
| Fetch timestamp | The timestamp in RFC2550/ARC condensed digits-only format indicating when the network fetch was started.  If appropriate the millisecond duration of the fetch is appended to the timestamp with a "+" character as separator. |
| SHA1 Digest | The SHA1 digest of the content only (headers are not digested). |
| Source Tag | The source tag inherited by the URI, if source tagging is enabled. |
| Annotations | If an annotation has been set, it will be displayed.  Possible annotations include: the number of times the URI was tried, the literal "lenTrunc" if the download was truncanted due to exceeding configured size limits, the literal "timeTrunc" if the download was truncated due to exceeding configured time limits or "midFetchTrunc" if a midfetch filter determined the download should be truncated. |
| warc | Nome do arquivo WARC/ARC em que o conteúdo rastreado foi salvo. Esse valor só será salvo se a propriedade logExtraInfo do bean loggerModule está definida como true. Essa informação registrada será salva no formato JSON. |

progress-statistics.log

Esse log é salvo pelo bean StatisticsTracker.  Em intervalos configuráveis, uma linha de log detalhando o progresso do rastreamento é gravada nesse arquivo.

| Nome do campo  | Descrição |
| ------------- | ------------- |
| timestamp  | Timestamp in ISO8601 format indicating when the log line was written.  |
| discovered  | Número de URIs descobertos até o momento. |
| queued | Número de URIs enfileirados. |
| downloaded | Número de URIs baixados até o momento. |
| doc/s(avg) | Número de documentos baixados por segundo desde a última snapshot. O valor entre parênteses é medido desde o início do rastreamento. |
| KB/s(avg) | Quantidade em kilobytes baixados por segundo desde a última snapshot. O valor entre parênteses é medido desde o início do rastreamento. |
| dl-failures | Número de URIs que o Heritrix não conseguiu baixar. |
| busy-thread | Number of toe threads busy processing a URI. |
| mem-use-KB | Quantidade de memória sendo usada pelo Java Virtual Machine. |
| heap-size-KB| The current heap size of the Java Virtual Machine. |
|congestion | The congestion ratio is a rough estimate of how much initial capacity, as a multiple of current capacity, would be necessary to crawl the current workload at the maximum rate available given politeness settings.  This value is calculated by comparing the number of internal queues that are progressing against those that are waiting for a thread to become available. |
| max-depth | O tamanho da fila Frontier com o maior número de URIs enfileirados.  |
| avg-depth | O tamanho habitual de todas as filas do Frontier. |

runtime-errors.log

Esse log captura exceções e erros inesperados que ocorrem durante o rastreamento, que podem ser resultantes de limitações do hardware (sem memória, embora esse erro possa ocorrer sem ser gravado neste log). No entanto, a maioria ocorre por causa de bugs do software, talvez no núcleo do Heritrix, mas provavelmente em uma de suas classes conectáveis.

uri-errors.log

Esse log armazena erros de tentativas de busca de URI, normalmente causados por URIs não existentes. Esse log normalmente é de interesse apenas para usuários avançados que tentam explicar comportamentos inesperados de rastreamento.

frontier.recover.gz

O arquivo frontier.recover.gz é um log gzipado de eventos Frontier que pode ser usado para restaurar o Frontier após uma falha.

## Configuração de Tarefas e Perfis

A criação de tarefas (Creating a Job) e perfis (Creating a Profile) é o primeiro passo no processo de usar o Heritrix para rastrear a web. Configurar tarefas e perfis é um trabalho um pouco mais complicado. A seção a seguir se aplica para a configuração tanto de tarefas quanto de perfis.

### Note

* Para editar um rastreamento em andamento, ver Editing a Running Job para mais informações.

Configurar uma tarefa ou um perfil involve a edição do arquivo `crawler-beans.cxml`. Esse arquivo é um arquivo de configuração Spring. O Framework Spring é usado para definir as propriedades de tarefas e perfis. Cada tarefa é definida por "beans" Spring que contêm dados de configuração para a tarefa. Essa seção aborda como configurar propriedades comuns de uma tarefa de rastreamento ou perfil e descreve as várias seções do arquivo `crawler-beans-cxml`.

A primeira seção do arquivo `crawler-beans-cxml` permite que o operador altere qualquer propriedade simples do bean, como a `metadata.operatorContactUrl`. Por exemplo, o Heritrix pode ser configurado para ignorar cookies com a seguinte substituição de configuração: 

### Alterar propriedade para ignorar cookies

```
<!-- overrides from a text property list -->
<bean id="simpleOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">
<property name="properties">
<value>

# This Properties map is specified in the Java 'property list' text format
# http://java.sun.com/javase/6/docs/api/java/util/Properties.html#load%28java.io.Reader%29

metadata.operatorContactUrl=http://www.archive.org
metadata.jobName=basic
metadata.description=Basic crawl starting with useful defaults
ignoreCookies=true

##..more?..##

</value>
</property>
</bean>
```

O bean "longerOverrides" está disponível para substituições mais longas ou complicadas. É usado para substituir propriedades que contêm múltiplos valores ou que podem ser alteradas com um bean. Por exemplo, vários seeds podem ser configurados com a seguinte configuração:

### Substituição dos valores dos seeds

```
<bean id="longerOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">

<property name="properties">

<props>
<prop key="seeds.textSource.value">

# URLS HERE
http://www.myhost1.net
http://www.myhost2.net
http://www.myhost3.net/pictures

    </prop>
</props>
</property>
```

## Cadeias de processamento

No nível de tarefa, uma tarefa de rastreamento do Heritrix possui três pipelines principais conhecidas como Cadeias de Processadores (aplicação sequencial de módulos de Processador trocáveis -- ver Configurações de Processador), com o Frontier atuando como um buffer entre as duas primeiras:

* Chain Candidates:

- Processa os URIs de rastreamento de entrada, decidindo se deve mantê-los (de acordo com o escopo) e se eles serão depositados no Frontier.

- Ver Candidate Chain Processors

* Frontier:

- Os URIs de rastreamento aceitos nesse rastreamento são armazenados aqui em ordem de prioridade, em um conjunto de filas distintas.

- Normalmente, há uma fila por "autoridade" (por exemplo, `exemplo.com:80`) e o gerenciamento de filas garante que o atraso de rastreamento desejado seja respeitado para cada fila.

- Ver Frontier

* Fetch Chain:

- Como os URIs de rastreamento são emitidos pelo Frontier, a cadeia de busca processa cada um deles e decide o que fazer, como baixar etc.

- Também realiza operações como extração de links.

- Ver Fetch Chain Processors

* Cadeia de disposição (Disposition Chain):

- Trata de pós-processamentos necessários após o termino da cadeia de busca.

- Por exemplo, é aqui que os recursos baixados são salvos no formato WARC.

- Ver Disposition Chain Processors

Todo URI retirado da fila Frontier é executado pelas cadeias de processamento. Os URIs são sempre processados na ordem mostrada no diagrama abaixo, a menos que um processador em particular exiba um erro fatal ou decida interromper o processamento do URI atual.


Cada cadeia de processamento é composta de zero ou mais processadores individuais. Por exemplo, o FetchChain pode incluir os processadores extractorCss e extractorJs. Em uma etapa de processamento, a ordem em que os processadores são executados é a ordem em que eles são listados no arquivo crawler-beans.cxml.

### Processadores da cadeia de candidatos (Candidate Chain Processors)

| Nome do processador  | Descrição |
| ------------- | ------------- |
| candidateScoper | Aplica regras de escopo a cada URI candidato. |
| preparer | Prepara os URIs aceitos para enfileirar no Frontier.  |

### Processadores da cadeia de busca (Fetch Chain Processors)

| Nome do processador  | Descrição | Nome da classe |
| ------------- | ------------- | ------------- |
| preparer | Prepara os URIs aceitos para enfileirar no Frontier.  | |
| preconditions | E  | |
| fetchDns | Busca DNS URIs.  | |
| fetchHttp |   | |
| extractorHttp |   | org.archive.modules.extractor.ExtractorHTTP |
| extractorHtml | Extrai links de conteúdos HTML.  | org.archive.modules.extractor.ExtractorHTML |
| extractorCss | Extrai links de conteúdos CSS.  | org.archive.modules.extractor.ExtractorCSS |
| extractorJs | Extrai links de conteúdos JavaScript. | org.archive.modules.extractor.ExtractorJs |
| extractorSwf | Extrai links de conteúdos Flash.  | org.archive.modules.extractor.ExtractorSWF |
| extractorPdf | Extrai links de conteúdos PDF.  | org.archive.modules.extractor.ExtractorPDF |
| fetchXml | Extrai links de conteúdos XML.  | org.archive.modules.extractor.ExtractorXML |

A maioria dos processadores de extração é pré-configurada no arquivo de configuração `crawler-beans.cxml` da tarefa sob o bean "fetchProcessors". Para adicionar um novo extrator, como um extrator XML/RSS, defina o bean e, em seguida, vincule-o ao bean "fetchProcessors". Exemplo de um bean extractorXml:

1. Defina o bean para o extrator XML/RSS:

``` 
<bean id="extractorXml" class="org.archive.modules.extractor.ExtractorXML"></bean>
```

2. Vincule o bean "extractorXml" com o bean "fetchProcessor":

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ************ ...extract outlinks from XML/RSS content.. ********** -->
<ref bean="extractorXml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

### Processadores da cadeia de disposição (Disposition Chain Processors)

| Nome do processador  | Descrição |
| ------------- | ------------- | 
| warcWriter| Grava os arquivos WARC de arquivamento. |
| preconditions | Envia os links de saída do URI buscado para a cadeia de candidatos para processamento.  |
| fetchDns | Atualiza estatísticas de rastreamento, estruturas de dados e decisões do Frontier.  |

### Configurações de processador

Os processadores têm configurações que efetuam rastreamentos. Cada processador pode ser ativado ou desativado. Se desativado, o Processador não será aplicado a nenhum URI. Os seguintes processadores possuem configurações padrão configuradas pelo Heritrix.

preparer

* preferenceDepthHops -  Number of hops (of any sort) from a seed up to which a URI has higher priority scheduling than any remaining seed. For example, if set to one, items one hop (link, embed, redirect, etc.) away from a seed will be scheduled with HIGH priority. If set to -1, no preferencing will occur, and a breadth-first search with seeds processed before discovered links will proceed. If set to zero, a purely depth-first search will proceed, with all discovered links processed before remaining seeds. Seed redirects are treated as one hop from a seed.

* preferenceEmbedHops - Number of embed hops (ERX) to bump to front of host queue.

* canonicalizationPolicy - Ordered list of URI canonicalization rules.  Rules are applied in the order listed from top to bottom.

* queueAssignmentPolicy - Define como atribuir URIs a filas. Pode atribuir por host, por IP, pela autoridade ordenada por SURT, pela autoridade ordenada por SURT truncada em um domínio atribuível mais alto e em um de um conjunto fixo de buckets (1k).

* uriPrecedencePolicy - Sets an integer precedence value on individual URIs when they are first submitted to a frontier for scheduling.  A URI's precedence directly affects which URI queue it is placed in, but does not affect a queue's precedence relative to other queues unless a queue-precedence-policy that consults URI precedence values is chosen.

* costAssignmentPolicy - Calcula um valor de 'custo' total para o CrawlURI fornecido.
preselector

* recheckScope - Recheck if URI is in scope. This is meaningful if the scope is altered during a crawl.  When URIs are added to queues they are checked against the scope.  Setting this value to true forces the URI to be checked against the scope when it comes out of the queue, possibly after the scope is altered.

* blockAll- Bloqueia todos os URIs de serem processados. É mais provável que seja usado em sobreposições para rejeitar facilmente determinados hosts de serem processados.

* blockByRegex - Block all URIs matching the regular expression from being processed.

* allowByRegex - Allow only URIs matching the regular expression to be processed.

preconditions

* ipValidityDurationSeconds - The minimum interval for which a dns-record will be considered valid (in seconds). If the record's DNS TTL is larger, that will be used instead.

* robotsValidityDurationSeconds- The time in seconds that fetched robots.txt information is considered valid. If the value is set to '0', then the robots.txt information will never expire.

* calculateRobotsOnly - Whether to calculate the robot's status of a URI, without actually applying any exclusions found. If true, excluded URIs will only be annotated in the crawl.log, but still fetched.

fetchDns

* acceptNonDnsResolves - Whether or not to fall-back to InetAddress resolution if a DNS lookup fails.  InetAddress resolution may use local 'hosts' files or other mechanisms.

* digestContent - Whether or not to perform an on-the-fly digest hash of retrieved content-bodies.

* digestAlgorithm - The algorithm (for example MD5 or SHA-1) used to perform an on-the-fly digest hash of retrieved content-bodies.


fetchHttp

* timeoutSeconds - Determina por quanto tempo uma solicitação HTTP aguardará um recurso responder. Essa configuração deve ser configurada para um valor alto.

* maxLengthBytes - Determina o número máximo de bytes para download por documento. Quando o limite é atingido, o documento será truncado. Por padrão, essa configuração é um valor muito grande (no intervalo exabyte), que teoricamente nunca será atingido.

* maxFetchKBSec - The maximum rate in KB/sec to use when fetching data from a server. The default of 0 means no maximum.
defaultEncoding - The character encoding to use for files that do not have one specified in the HTTP response headers. The default is ISO-8859 -1.

* shouldFetchBodyRule- DecideRules applied after receipt of HTTP response headers but before download of the HTTP body. If any rule returns FALSE, the fetch is aborted. Prerequisites such as robots.txt are excluded from filtering, i.e. they cannot be midfetch aborted.

* soTimeoutMs - If the socket is unresponsive for this number of milliseconds, the request is cancelled.  Setting the value to zero (no timeout) is not recommended as it could hang a thread on an unresponsive server. This timeout is used to time out socket opens and socket reads. Make sure this value is less than timeoutSeconds for optimal configuration.  This ensures at least one retry read.
sendIfModifiedSince - Send If-Modified-Since header, if previous Last-Modified fetch history information is available in URI history.
sendIfNoneMatch - Send If-None-Match header, if previous Etag fetch history information is available in URI history.

* sendConnectionClose - Send Connection: close header with every request. - w3.org connection header documentation

* sendReferer- The Referer header contains the location the crawler came from.  This is the page the current URI was discovered in. The Referer is usually logged on the remote server and can be of assistance to webmasters trying to figure out how a crawler got to a particular area on a site.

* sendRange- Send the Range header when there is a limit on the retrieved document size.  This is for politeness purposes.  The Range header states that only the first n bytes are of interest.  It is only pertinent if maxLengthBytes is greater than zero.  Sending the Range header results in a 206 Partial Content status response, which is better than cutting the response mid-download. On rare occasion, sending the Range header will generate 416 Request Range Not Satisfiable response.

* ignoreCookies - Desativa o manuseio de cookies.

* sslTrustLevel- The SSL certificate trust level. The range is from the default open (trust all certs including expired, selfsigned, and those for which we do not have a CA) through loose (trust all valid certificates including selfsigned), normal (all valid certificates not including selfsigned) and strict (Cert is valid and DN must match servername).

* acceptHeaders - Accept Headers to include in each request. Each must be the complete header, e.g., Accept-Language: en.

* httpBindAddress- Local IP address or hostname to use when making connections (binding sockets). When not specified, uses default local address(es).

* httpProxyHost - O endereço IP do host proxy.

* httpProxyPort - A porta do proxy.

* digestContent - Whether or not to perform an on-the-fly digest hash of retrieved content-bodies.

* digestAlgorithm - Specifies which algorithm (for example MD5 or SHA-1) is used to perform an on-the-fly digest hash of retrieved content-bodies.

extractorHtml

* extractJavascript - If true, in-page Javascript is scanned for strings that appear to be URIs. This typically finds both valid and invalid URIs.  Attempts to fetch the invalid URIs can generate webmaster concern over odd crawler behavior. Default is true.
extractValueAttributes- If true, strings that look like URIs found in unusual places (such as form VALUE attributes) will be extracted. This typically finds both valid and invalid URIs.  Attempts to fetch the invalid URIs may generate webmaster concerns over odd crawler behavior. Default is true.

* ignoreFormActionUrls - Se definido como "true", os URIs que aparecem como o atributo ACTION nos FORMULÁRIOS HTML são ignorados. A definição padrão é "false"

* extractOnlyFormGets - Se definido como "true", somente URIs de AÇÃO com um MÉTODO GET (explícito ou implícito) serão extraídos. A definição padrão é "true".

* treatFramesAsEmbedLinks- Se definido como "true", os links SRF do FRAME/IFRAME são tratados como recursos incorporados (como IMG, 'E' hop-type). Caso contrário, eles são tratados como links de navegação.  A definição padrão é "true".

* ignoreUnexpectedHtml - Se definido como "true", os URIs que terminam em extensões não HTML comuns (como .gif) não serão verificados como se fossem HTML. A definição padrão é "true".

* maxElementLength - Comprimento máximo de um elemento HTML.

* maxAttributeNameLength - Comprimento máximo de um nome de atributo HTML.

* maxAttributeValueLength - Comprimento máximo de um valor de atributo HTML.

warcWriter

* compress - Se essa configuração for definida como "true", conteúdo do arquivo WARC será compactado. Observe que a compactação se aplica a cada item de conteúdo armazenado no WARC.

* prefix - Prefixo do nome do arquivo WARC.

* maxFileSizeBytes- Essa configuração determina o tamanho máximo em bytes para cada arquivo WARC. Quando o arquivo WARC atingir esse tamanho, URIs não serão mais gravados nele e outro arquivo WARC será criado para manipular os URIs restantes. Esta configuração não é estritamente rígida: se URIs extremamente grandes estiverem sendo baixados, o arquivo WARC poderá exceder esse limite. Os itens de conteúdo armazenados nos arquivos WARC nunca são divididos entre os WARCs.

* storePaths- Essa configuração é uma lista de caminhos nos quais os arquivos WARC serão gravados. Se mais de um caminho for especificado, uma abordagem round-robin será usada para escolher o caminho. Essa configuração é segura para ser alterarada durante um rastreamento.

* poolMaxActive- O Heritrix mantém vários arquivos WARC prontos para receberem documentos baixados. Essa abordagem é usada para evitar que o WARC seja uma restrição em um ambiente multithread. Essa configuração estabelece o número máximo desses arquivos para manter-se pronto. O valor padrão é cinco. Para pequenos rastreamentos que você deseja limitar a um único arquivo WARC, essa configuração deve ser definida como um.

* maxWaitForIdleMs - Controla quanto tempo um thread espera por um gravador reutilizável antes de considerar a criação de um novo. Se a criação não for permitida, os threads aguardarão indefinidamente a disponibilização de um gravador.

* skipIdenticalDigests- Whether to skip the writing of a record when URI history information is available and indicates the prior fetch had an identical content digest.  Default is false.

* maxTotalBytesToWrite - Total file bytes to write to disk. Once the size of all files on disk has exceeded this limit, this processor will stop the crawler. A value of zero means no upper limit.

* directory - O diretório no qual os storePaths serão configurados.

* writeRequests -  Decide se deve gravar registros do tipo de solicitação. A definição padrão é "true".

* writeMetadata - Decide se deve gravar registros do tipo de metadados. A definição padrão é "true".

* writeRevisitForIdenticalDigests- Decide se deve gravar registros do tipo revisit quando o histórico de um URI indica que a busca anterior tinha um resumo de conteúdo idêntico. A definição padrão é "true".

* writeRevisitForNotModified - Decide se deve gravar registros do tipo revisit quando uma resposta 304-Not Modified é recebida. A definição padrão é "true".


candidates

* seedsRedirectNewSeeds - If enabled, any URI found because a seed redirected to it (original seed returned 301 or 302), will also be treated as a seed.

disposition

* delayFactor - Quantos múltiplos do último tempo transcorrido para aguardar antes de entrar em contato com o mesmo servidor.

* minDelayMs- O tempo mínimo para aguardar após uma solicitação ser concluída antes de entrar em contato novamente com o mesmo servidor. Este valor sobrepõe o delayFactor.

* respectCrawlDelayUpToSeconds - Whether to respect a Crawl-Delay (in seconds) provided by the site's robots.txt.

* maxDelayMs- A quantidade máxima de tempo para esperar após uma solicitação ser concluída antes de entrar em contato novamente com o mesmo servidor. Este valor substitui o delayFactor.

* maxPerHostBandwidthUsageKbSec - O uso máximo de largura de banda por host.

### Rastreamento de estatíticas 

Há vários módulos de rastreamento de estatísticas que podem ser anexado a um rastreamento. Atualmente, apenas um é fornecido pelo Heritrix. O Spring bean `statisticsTracker` que vem com o Heritrix cria o arquivo `progress-statistics.log` e fornece a IUW com dados para exibir informações de progresso sobre o rastreamento. É altamente recomendável que qualquer rastreamento executado pela IUW use esse bean.

### Regras de canonização da URI

O Heritrix controla os URIs duplicados para não rastrear conteúdo redundante. No entanto, os URIs podem ser compostos de várias maneiras, mesmo que a página apontada pelos URIs seja idêntica. Por exemplo, a página http://www.archive.org/index.html é a mesma página que http://WWW.ARCHIVE.ORG. Para resolver esse problema, o Heritrix canoniza os URIs antes de serem comparados quanto à desduplicação. A canonização envolve a aplicação de uma série de regras. Por exemplo, uma regra de canonização transforma todos os URIs em minúsculas. Outra regra retira o prefixo 'www' dos domínios.

O bean `canonicalizationPolicy` permite especificar regras de canonização e a ordem em que elas são executadas.

Para registrar o processo de canonização, adicione a linha:

`org.archive.modules.canonicalize.RulesCanonicalizationPolicy.level = FINER`

para o `conf/logging.properties`.

As regras de canonização NÃO são executadas se o URI que está sendo verificado for o resultado de um redirecionamento. Isso é feito pelo seguinte motivo: suponha que uma regra de canonização que equalize o http://www.archive.org para http://archive.org esteja em vigor. Se o rastreador encontrar o http://archive.org, mas o servidor no archive.org direcionar para o http://www.archive.org/, o Heritrix vai pensar que o http://www.archive.org já foi rastreado. Esse problema é evitado, ignorando as regras de canonização para redirecionamentos.

Alterar a canonização de URI também afeta a Wayback

Observe que, se as regras de Canonicalização de URI forem modificadas, isso também poderá afetar a experiência de reprodução. Deve-se garantir que as configurações da Wayback e do Heritrix sejam consistentes, caso contrário, o mecanismo Wayback pode não conseguir resolver os URLs nas páginas da Web para o recurso canonicalizado correspondente.

Caso de uso de regra de canonização URI: remoção de IDs de sessão específicos do site

Neste caso de uso, assumimos que um site está retornando URIs com uma chave de ID de sessão de cid. Por exemplo, . Suponha que o ID da sessão tenha sempre 32 caracteres. Além disso, suponha que o cid aparece sempre no final do URI. Isso apresenta um problema porque o mesmo URI será capturado repetidamento devido aos IDs de sessão diferentes.

A solução é adicionar uma segunda política de canonização de URI derivada da padrão. A nova política incluirá uma regra de expressão regular que filtra o ID da sessão e que é anexada a uma sobreposição de sheet. Uma sobreposição de sheet é uma maneira de substituir propriedades de beans com base em diferentes conjuntos de valores. A sobreposição de sheet criada será configurada de forma a aplicar-se apenas a URIs com um domínio específico. O exemplo abaixo mostra a configuração do Spring usada para obter esse efeito.

Isso já está no cxml padrão:

```
 <!-- SHEETOVERLAYMANAGER: manager of sheets of contextual overlays
      Autowired to include any SheetForSurtPrefix or
      SheetForDecideRuled beans -->
 <bean id="sheetOverlaysManager" autowire="byType"
  class="org.archive.crawler.spring.SheetOverlaysManager">
 </bean>
 ```
 
 Isso é comentado no cxml padrão e precisa ser descomentado:
 
 ``` <!-- CANONICALIZATION POLICY -->
 <bean id="canonicalizationPolicy"
  class="org.archive.modules.canonicalize.RulesCanonicalizationPolicy">
  <property name="rules">
   <list>
    <bean class="org.archive.modules.canonicalize.LowercaseRule" />
    <bean class="org.archive.modules.canonicalize.StripUserinfoRule" />
    <bean class="org.archive.modules.canonicalize.StripWWWNRule" />
    <bean class="org.archive.modules.canonicalize.StripSessionIDs" />
    <bean class="org.archive.modules.canonicalize.StripSessionCFIDs" />
    <bean class="org.archive.modules.canonicalize.FixupQueryString" />
   </list>
  </property>
 </bean>
 ```
 
 E algo do tipo precisa ser adicionado:
 
 ```
  <bean id="altCanonicalizationPolicy"
  class="org.archive.modules.canonicalize.RulesCanonicalizationPolicy"
  parent="canonicalizationPolicy" autowire-candidate="false">
  <property name="rules">
   <list merge="true">
    <bean class="org.archive.modules.canonicalize.RegexRule">
     <property name="regex" value="^(.+)(?:cid=0-9a-zA-Z{32})?$"/>
    </bean>
   </list>
  </property>
 </bean>

 <bean class='org.archive.crawler.spring.SurtPrefixesSheetAssociation'>
  <property name='surtPrefixes'>
   <list>
    <value>http://(z,y,</value>
   </list>
  </property>
  <property name='targetSheetNames'>
   <list>
    <value>canonicalizationPolicySheet</value>
   </list>
  </property>
 </bean>
 
 <bean id='canonicalizationPolicySheet' class='org.archive.spring.Sheet'>
  <property name='map'>
   <map>
    <entry key='preparer.canonicalizationPolicy'>
     <ref bean='altCanonicalizationPolicy'/>
    </entry>
   </map>
  </property>
 </bean>
 ```
 
Para ver a regra em operação, defina o nível de logging para `org.archive.crawler.url.Canonicalizer` em `logging.properties`. Estude o output e ajuste seu regex de acordo.

## Credenciais 

Credenciais podem ser adicionadas para que o Heritrix consiga obter acesso a áreas de sites que exigem autenticação. As credenciais são configuradas no arquivo de configuração do Spring, `crawler-beans.cxml`. O exemplo a seguir mostra uma credencial configurada.

```
<bean id="credential"
   class="org.archive.modules.credential.HttpAuthenticationCredential">

    <property name="domain">
        <value>
            domain
        </value>
    </property>

    <property name="realm">
        <value>
            myrealm
        </value>
    </property>

    <property name="login">
        <value>
            mylogin
        </value>
    </property>

    <property name="password">
        <value>
            mypassword
        </value>
    </property>

 </bean>
 ```
 
Uma das configurações de uma credencial é seu domínio. Portanto, é possível criar todas as credenciais em nível global. No entanto, como isso pode causar um excesso de verificações desnecessárias de credenciais, recomenda-se que as credenciais sejam adicionadas a uma substituição de domínio. Dessa forma, a credencial só é verificada quando o domínio relevante está sendo rastreado.

O Heritrix oferece dois tipos de autenticação: RFC2617 (Autenticação BASIC e DIGEST) e POST e GET de um formulário HTML.

### Armazenamento de credenciais

O Armazenamento de Credenciais contém o http ou informações de login de formulário. Deve ser configurado no arquivo `crawler-beans.cxml`.

Este é um exemplo de uma CredentialStore vazia:

```
<bean id="credentialStore"
      class="org.archive.modules.credential.CredentialStore">
</bean>
```

No entanto, para que funcione, sua propriedade de mapa de 'credenciais' deve ser preenchida com instâncias de credenciais adequadas. Por exemplo, se você tiver um bean HtmlFormCredential definido em outro lugar com o id 'formCredential', o seguinte resultará em uma CredentialStore incluindo essa Credencial:

```
<bean id="credentialStore"
       class="org.archive.modules.credential.CredentialStore">
 <property key="credentials">
  <map>
   <entry key="formCredential" value-ref="formCredential" />
  </map>
 </property>
</bean>
```


### Formulário HTML GET ou POST

Para usar a credencial `GET` ou `POST` do Formulário HTML, forneça um `domínio`, um `método http`, um `login-uri` e `itens de formulárioP`.

Antes de um URI ser agendado para rastreamento, o Heritrix procura por pré-condições. Exemplos de pré-condições incluem a captura do registro DNS do servidor que hospeda o URI e a busca do arquivo robots.txt. As credenciais do formulário HTML também são processadas como pré-condição. Se houver credenciais de formulário HTML para um determinado CrawlServer no repositório de credenciais, o URI especificado no campo de login de credencial do formulário HTML será agendado como uma pré-condição para o site, após as condições prévias do DNS e do robots.txt.

domain

Ver o domínio (BASIC AND DIGEST Auth).

login-uri

O login-uri é um URI relativo ou absoluto ao qual o Formulário HTML é enviado. Não é necessariamente a página que contém o formulário HTML; mas sim o ACTION URI ao qual o formulário é submetido.

form-items

Itens de formulário (form-items) são uma listagem de pares de chave/valor de formulário HTML. O botão de envio geralmente deve ser incluído nos itens de formulário.

Uma configuração de formulário HTML `GET` ou `POST` é ilustrada abaixo. (Observe que esse bean deve aparecer no mapa 'credentials' da CredentialStore, seja por meio de uma referência de bean ou por estar definido como inline.)

```
<bean id="credential"
   class="org.archive.modules.credential.HtmlFormCredential">

    <property name="domain" value="example.com" />

    <property name="login-uri" value="http://example.com/login"/>

    <property name="form-items">
        <map>
            <entry key="login" value="mylogin"/>
            <entry key="password" value="mypassword"/>
            <entry key="submit" value="submit"/>
        </map>
    </property>
</bean>
```

Observação

* Para um site com uma credencial de Formulário em HTML, um login é realizado em relação a todas as credenciais de login do formulário HTML listado após as condições prévias do DNS e do robots.txt serem atendidas. O rastreador só visualizará sites que tenham credenciais de formulário HTML de uma perspectiva conectada. Não há, atualmente, uma maneira de um único trabalho do Heritrix rastrear um site em um estado não autenticado e, em seguida, rastrear novamente o site em um estado autenticado. (Isso teria que ser feito em dois lançamentos de tarefa configurados separadamente.) 

* O login do formulário é executado apenas uma vez. O Heritrix continua rastreando, independentemente de o login ser bem-sucedido. Não há como dizer ao Heritrix para repetir a autenticação se a primeira tentativa não for bem-sucedida. Também não existe um meio para o rastreador reportar autenticações com sucesso ou falhas. O operador de rastreamento deve examinar os logs para determinar se a autenticação foi bem-sucedida.
 
* Os formulários de login de alguns sites podem ter itens de formulário com nomes dinâmicos ou campos ocultos extras necessários, cujo valor requerido é alterado para cada visitante. Este mecanismo HtmlFormCredential não tem suporte para enviar esses formulários com êxito.

### Logging

Para habilitar o log do console de texto de interações de autenticação, defina os níveis de log `FetchHTTP` e `PreconditionEnforcer` para "fine".

```
org.archive.crawler.fetcher.FetchHTTP.level = FINE
org.archive.crawler.prefetch.PreconditionEnforcer.level = FINE
```

Isso é feito editando o arquivo `logging.properties` do diretório conf.

### RFC2617 (autenticação BASIC e DIGEST)

Para usar a credencial RFC2617, forneça um domínio, realm, nome de usuário e senha.

A maneira como a autenticação RFC2617 funciona no Heritrix é que, em resposta a um código de resposta 401 (não autorizado), o Heritrix usará uma chave composta do domínio e do realm para fazer uma pesquisa em seu Repositório de Credenciais. Se uma correspondência for encontrada, a credencial será carregada no CrawlURI, que será marcado para uma nova tentativa imediata. 

Quando a nova tentativa é feita, as credenciais encontradas são adicionadas à solicitação.  Se a solicitação for bem-sucedida com um código de resposta 200, as credenciais serão promovidas para o servidor CrawlServer e todas as solicitações subsequentes, feitas no CrawlServer, fornecerão a credencial de forma preventiva. Se a credencial falhar com um código de resposta 401, não haverão novas tentivas com o CrawlURI.

domain

Domain (domínio) é o URI de raiz canônico do RFC2617; é o nome ou a autoridade do URI do CrawlServer (domínio mais porta se diferente da porta 80). Exemplos de domínios são: 'www.archive.org' ou 'www.archive.org:8080'.

realm

O realm (território) é definido na FAQ RFC2617. A cadeia do realm deve corresponder exatamente ao nome do realm apresentado no desafio de autenticação atendido pelo servidor da web.

Uma configuração de credencial RFC2617 é mostrada abaixado:

```
<bean id="credential"
class="org.archive.modules.credential.HttpAuthenticationCredential">


<property name="domain">
<value>
domain
</value>
</property>


<property name="realm">
<value>
myrealm
</value>
</property>


<property name="login">
<value>
mylogin
</value>
</property>


<property name="password">
<value>
mypassword
</value>
</property>


</bean>
```

Observação

Apenas um realm por domain de credencial é permitido. Consulte Logon (HTTP POST, Basic Auth, etc.) para mais informações.

## Criação de Tarefas e Perfis

Esta seção fornece detalhes sobre a criação de tarefas e perfis.

### Criando uma tarefa

Uma tarefa pode ser criada a partir de três maneiras:

1. Com base em uma tarefa existente

Essa opção permite que uma nova tarefa seja criada com base em uma tarefa existente, independentemente de a tarefa ter sido rastreada ou não. Essa opção pode ser útil para repetir rastreamentos ou recuperar rastreamentos que tiveram problemas. Veja Recovery of Frontier State e frontier.recover.gz. Para criar uma tarefa baseada em um trabalho existente, use o botão "copy" na página do trabalho original.

2. Com base em um perfil.

Essa opção permite que uma nova tarefa seja criada com base em um perfil existente. Para criar uma tarefa baseada em um perfil, use o botão "copy" na página do perfil.

3. Com base no perfil padrão.

Essa opção cria uma tarefa de rastreamento com base no perfil padrão. Para criar uma tarefa a partir do perfil padrão, insira um nome de trabalho na tela do controle principal e clique no botão "create".

Observação

* Mudanças feitas em tarefas ou perfis não afetam tarefas derivadas.

* Tarefas baseadas no perfil padrão fornecido pelo Heritrix não estão prontos para serem executados como estão. O `metadata.operatorContactUrl` precisa ser configurado com um valor válido.

### Criando um perfil

Perfis podem ser criados a partir de tarefas existentes ou outros perfis. Na página de tarefa ou perfil, insira o nome do novo perfil e marque a caixa "as profile". Em seguida, clique no botão "copy".

Para modificar o comportamento do novo perfil, consulte Configurando Tarefas e Perfis.

Observação

As alterações feitas no perfil ou na tarefa não afetam perfis derivados.

## Além da Interface de Usuário

Embora seja possível fazer muitas coisas com a IUW do Heritrix, há muitos recursos disponíveis fora dela.

### Recuperação de rastreamento

Durante a operação normal, o Heritrix Frontier mantém um diário. O diário é mantido no diretório de log e é nomeado `frontier.recovery.gz`. Se ocorrer uma falha durante um rastreamento, o diário `frontier.recover.gz` poderá ser usado para recriar o status aproximado do rastreador no momento da falha. Em alguns casos, a recuperação pode demorar, mas normalmente é muito mais rápida do que repetir o rastreamento travado.

A utilização desse processo inicia um rastreamento totalmente novo, com a mesma configuração (ou modificada). No entanto, esse novo rastreamento fará um desvio maior no início, no qual é usado as saídas frontier-recover.gz do rastreamento anterior para simular o status da fronteira (URIs descobertos, URIs enfileirados) do rastreamento anterior. Todos os ARC/WARCs, logs e pontos de verificação do rastreamento anterior seriam deixados de lado, retendo apenas os logs e ARC/WARCs como um registro do rastreamento até o momento.

Quaisquer arquivos ARC/WARC que existam com o sufixo `.open` não foram fechados corretamente pela execução anterior e podem incluir dados corrompidos/truncados no último registro parcial. Os arquivos com sufixo `.warc.gz.open` podem ser renomeados para `.warc.gz`, mas considere a possibilidade de validar tais ARC/WARCs (zcat'ando o arquivo para/dev/null para verificar a validade do gzip ou outras ferramentas ARC/WARC para registro completo) antes de remover o sufixo ".open".

### Recuperação completa

Para executar o processo de recuperação, reinicie o rastreador com falha e copie o arquivo `frontier.recover.gz` no Action Directory. Em seguida, reinicie o rastreamento. O Heritrix carregará automaticamente o arquivo de recuperação e começará a colocar seus URIs no Frontier para rastreamento.

Se um arquivo `.recover.gz` estiver sendo usado, apenas um arquivo completo deve ser usado. (Isso é para que o diretório de ação processando um arquivo de cada vez possa fazer a primeira passagem completa de 'includes', em seguida, a passagem completa de 'schedules', de um arquivo. O fornecimento de vários arquivos `.recover.gz` em série resultará em um ciclo includes/schedule, includes/schedule, etc., que não produzirá o efeito desejado no frontier.)

Enquanto o arquivo estiver sendo processado, qualquer ponto de verificação (manual ou automático) não será um snapshot válido do estado do rastreador. (O processo de log de recuperação do frontier ocorre por meio de um thread/caminho separado, fora do sistema de verificação mais recente.). Somente quando o processamento do arquivo for concluído (arquivo movido para 'concluído') o rastreador estará em um estado de verificação confiável.

Depois que os URIs começarem a aparecer nas filas (quando a recuperação entrar na passagem de 'agendamentos'), o rastreador poderá ser despausado para começar a buscar URIs enquanto o restante da passagem de recuperação 'agendamentos' continuar. 

No entanto, a nota acima sobre pontos de verificação ainda se aplica: um ponto de verificação preciso só ocorrerá quando o processamento de arquivos de recuperação do frontier for concluído.

Além disso, despausar o rastreamento dessa maneira pode resultar na redescoberta de alguns URIs por meio de novos caminhos antes que a descoberta original seja reproduzida por meio do processo de recuperação. (Muitos rastreamentos podem não se importar com esse pequeno desvio do estado de rastreamento recuperado, mas se o escopo for muito dependente do caminho ou do hop, isso pode causar diferenças no que é incluído no escopo).

possibilidade de criação de Avisos

Observação: feeding the entire frontier back to the crawler is likely to produce many "Problem line" warnings in the job log. Some operators find it useful to allow the entire recovery file to be ingested by the crawler before attempting to resume (unpause), to help isolate this chatter, and to minimize generating duplicate crawldata during recovery.

### split recover

Uma maneira alternativa de executar o processo de recuperação é ilustrada abaixo. Eliminar linhas irrelevantes logo no início (fora do processo de recuperação) pode permitir que o processo de recuperação seja concluído mais rápido do que o processo padrão. Também permite que o processo proceda de vários arquivos, em vez de um só, portanto, pode fornecer uma indicação de progresso melhor em andamento e chances de checkpoint a recuperação.

Para executar o processo de recuperação alternativo:

1. move aside prior logs and ARCs/WARCs as above
2. Abrir novamente o rastreamento com falha
3. Dividir qualquer arquivo de origem `frontier.recover.gz` usando comandos como os seguintes:

```
zcat frontier.recover.gz | grep '^Fs' | gzip > frontier.include.gz
zcat frontier.recover.gz | grep '^F+' | gzip > frontier.schedule.gz
```

1. Construa e inicie a tarefa que havia falhado (com a mesma configuração). A tarefa estará pausada.
2. Mova o(s) arquivo(s) `frontier.include.gz` para o diretório "action". O diretório está localizado no mesmo nível na hierarquia da estrutura de arquivos que o diretório bin. (Se houver muitos arquivos, é possível movê-los todos juntos de uma só vez, ou em pequenos lotes para monitorar melhor o progresso. No momento em que todos os arquivos previamente apresentados forem processados - ou seja, movidos para o diretório 'done', é possível fazer um checkpoint válido.
3. O progesso dessa fase 'includes' pode ser acompanhando visualizando a interface do usuário da web ou pelo contador `discovered` pelo `progress-statistics.log` 
4. Quando todos `.includes` terminarem de carregar, o processo pode ser repetido com todos os logs `.schedule`.
5. Se houver um número muito grande de URIs na contagem da fila (vários milhares), o rastreamento pode ser despausado para permitir que o novo rastreamento continue paralelamente ao enfileiramento de URIs mais antigos.

Você pode mover todos os arquivos `.include` e `.schedule` para o diretório "action" antes de iniciar, se tiver certeza de que a ordenação lexicográfica de seus nomes funcionará corretamente (mostrar todos os {{.include}}s primeiro e o {{. schedule}}s na mesma ordem que o rastreamento original). No entanto, isso deixa pouca oportunidade para ajustar/checkpoint o processo: o diretório "action" irá descobri-los e processá-los todos em um loop fechado.

Observação: Para ter certeza do sucesso e do atual status do rastreamento contra qualquer tipo de possível erros IO/format, em grandes recuperações de milhões de registros, é melhor aguardar a conclusão de cada etapa antes de mover um arquivo ou despausar a tarefa. Em vez de analisar as estatísticas de progresso, aguarde até que o arquivo seja movido de "action" para "action/done". Em seguida, adicione o segundo arquivo. Espere novamente. Finalmente, despause o rastreador.

Uma recuperação de 100M URIs pode levar dias, então, por favor, seja paciente.

## Processadores de Redução de Duplicação

### Redução de duplicação de URL-Agnostic 

Para configurar a deduplicação independente de URL de conteúdo idêntico, adicione esses beans de nível superior ao seu crawler-beans.cxml:

```
  <!-- optional, will use the main bdb module if omitted, just like old dedup -->
  <bean id="historyBdb" class="org.archive.bdb.BdbModule" autowire-candidate="false">
   <property name="dir" value="history" />
  </bean>

  <bean id="contentDigestHistory" class="org.archive.modules.recrawl.BdbContentDigestHistory">
   <property name="bdbModule">
    <ref bean="historyBdb" />
   </property>
  </bean>
  ```
  
  And then insert these beans into your disposition chain, sandwiching the warcWriter:
  
  ```
   <bean id="dispositionProcessors" class="org.archive.modules.DispositionChain">
   <property name="processors">
    <list>
==>    <bean class="org.archive.modules.recrawl.ContentDigestHistoryLoader" />
     <!-- write to aggregate archival files... -->
     <ref bean="warcWriter"/>
==>    <bean class="org.archive.modules.recrawl.ContentDigestHistoryStorer" />
     <!-- ...send each outlink candidate URI to CandidateChain,
          and enqueue those ACCEPTed to the frontier... -->
     <ref bean="candidates"/>
     <!-- ...then update stats, shared-structures, frontier decisions -->
     <ref bean="disposition"/>
     <!-- <ref bean="rescheduler" /> -->
    </list>
   </property>
  </bean> 
  ```
  
  ```
  Legacy Duplication Reduction Configuration
  ```
  
A partir da versão 1.12.0, vários Processadores podem cooperar para transportar o histórico de conteúdo do URI entre os rastreamentos (consulte JavaDocs do pacote `org.archive.crawler.processor.recrawl`). Isso reduz a quantidade de material duplicado baixado ou armazenado em rastreamentos posteriores. Consulte Deduplicação (Redução de Duplicação) para detalhes de configuração de desduplicação do H1. No H3, use o arquivo `crawler-beans.cxml` para configurar a deduplicação e adicione os seguintes processadores, conforme descrito abaixo.

## Configurar armazenamento de histórico de URI persistente

Essas configurações devem ser adicionadas a qualquer rastreamento que forneça informações de busca de histórico para futuras deduplicações (como o primeiro de dois rastreamentos ou todos os rastreamentos de uma série em andamento).

(1) Defina um bean nomeado para `FetchHistoryProcessor`:

```
<bean id="fetchHistoryProcessor" class="org.archive.modules.recrawl.FetchHistoryProcessor">
 <!-- <property name="historyLength" value="2" /> -->
</bean>
```

(2) Defina um bean nomeado para `PersistStoreProcessor`:

```
<bean id="persistStoreProcessor" class="org.archive.modules.recrawl.PersistStoreProcessor">
</bean>
```

(3) Adicione esses beans de processador às cadeias de processamento relevantes.

O *fetchHistoryProcessor* deve ser adicionado ao *FetchChain* depois dos outros processadores de busca. O persistStoreProcessor deve ser adicionado depois de qualquer gravador no DispositionChain. A melhor forma de fazer isso é usando marcadores "ref" para se referir aos beans processadores acima nas definições do bean *FetchChain* e *DispositionChain*.

Exemplo em um FetchChain comum:

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
  <property name="processors">
   <list>
    <!-- recheck scope, if so enabled... -->
    <ref bean="preselector"/>
    <!-- ...then verify or trigger prerequisite URIs fetched, allow crawling... -->
    <ref bean="preconditions"/>
    <!-- ...fetch if DNS URI... -->
    <ref bean="fetchDns"/>
    <!-- ...fetch if HTTP URI... -->
    <ref bean="fetchHttp"/>
=>    <ref bean="fetchHistoryProcessor" />
    <!-- ...extract oulinks from HTTP headers... -->
    <ref bean="extractorHttp"/>
    <!-- ...extract oulinks from HTML content... -->
    <ref bean="extractorHtml"/>
    <!-- ...extract oulinks from CSS content... -->
    <ref bean="extractorCss"/>
    <!-- ...extract oulinks from Javascript content... -->
    <ref bean="extractorJs"/>
    <!-- ...extract oulinks from Flash content... -->
    <ref bean="extractorSwf"/>
   </list>
  </property>
</bean>
```

Exemplo em um DispositionChain comum:

```
<bean id="dispositionProcessors" class="org.archive.modules.DispositionChain">
  <property name="processors">
   <list>
    <!-- write to aggregate archival files... -->
    <ref bean="warcWriter"/>
=>  <ref bean="persistStoreProcessor" />
    <!-- ...send each outlink candidate URI to CandidatesChain,
         and enqueue those ACCEPTed to the frontier... -->
    <ref bean="candidates"/>
    <!-- ...then update stats, shared-structures, frontier decisions -->
    <ref bean="disposition"/>
   </list>
  </property>
</bean>
```

Feito isso, as informações do histórico URI de interesse para os rastreamentos posteriores são mantidas pelo ambiente BDB padrão do rastreamento (conforme gerenciado pelo BdbModule, residindo no disco no diretório 'state').

### Configurar o carregamento de histórico de URI persistente para rastreamentos subsequentes

Siga as etapas abaixo para qualquer tipo de rastreamento que tire proveito da deduplicação, como o segundo de dois rastreamentos, ou todos os rastreamentos de uma série (menos o primeiro).

(1) Defina um bean nomeado para `PersistLoadProcessor`:

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
</bean>
```

Conforme configurado aqui, as informações do histórico de URI serão carregadas a partir do ambiente BDB padrão do rastreamento (conforme gerenciado pelo BdbModule, residindo no disco no diretório 'state').

(2) Se ainda não existir, defina um bean nomeado para o `FetchHistoryProcessor`:

```
<bean id="fetchHistoryProcessor" class="org.archive.modules.recrawl.FetchHistoryProcessor">
 <property name="historyLength" value="10" />
</bean>
```

(3) Assegure-se de que esses beans de processador estejam nas cadeias de processamento apropriadas, nos locais certos. Adicione as tags `ref` dos processadores acima ao bean *FetchChain*. Adicione o processador *persistLoadProcessor* após o bean das pré-condições. Adicione o *fetchHistoryProcessor* após outros beans de busca.

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
  <property name="processors">
   <list>
    <!-- recheck scope, if so enabled... -->
    <ref bean="preselector"/>
    <!-- ...then verify or trigger prerequisite URIs fetched, allow crawling... -->
    <ref bean="preconditions"/>
=>    <ref bean="persistLoadProcessor"/>
    <!-- ...fetch if DNS URI... -->
    <ref bean="fetchDns"/>
    <!-- ...fetch if HTTP URI... -->
    <ref bean="fetchHttp"/>
=>    <ref bean="fetchHistoryProcessor"/>
    <!-- ...extract oulinks from HTTP headers... -->
    <ref bean="extractorHttp"/>
    <!-- ...extract oulinks from HTML content... -->
    <ref bean="extractorHtml"/>
    <!-- ...extract oulinks from CSS content... -->
    <ref bean="extractorCss"/>
    <!-- ...extract oulinks from Javascript content... -->
    <ref bean="extractorJs"/>
    <!-- ...extract oulinks from Flash content... -->
    <ref bean="extractorSwf"/>
   </list>
  </property>
</bean>
```

### opção *preloadSource*

Se você deseja extrair o histórico de URIs persistentes de uma origem não padrão, a propriedade `preloadSource` pode ser usada no PersistLoadProcessor. Seu valor deve ser o caminho para o diretório `state` de outro rastreamento anterior ou um caminho ou URI para o log de persistência de outro rastreamento anterior (`persistlog.txtser.gz`) no caso do PersistLogProcessor ser usado, o que não é recomendado no H3 e não descrito acima.

O pré-carregamento verifica o diretório "state" antigo ou registra na inicialização, carregando o diretório "state" do rastreamento atual com as informações do histórico. Isso pode ser uma etapa demorada, o que atrasa o início do rastreamento. Observe que preloadSource *não* deve ser o diretório "state" do rastreamento atual ou ocorrerá um erro.

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
 <property name="preloadSource" value="/Users/me/Documents/heritrix-3.0.0-SNAPSHOT/jobs/originalCrawl/state"/>
</bean>
```

OU

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
 <property name="preloadSource" value="file:///Users/me/Documents/heritrix-3.0.0-SNAPSHOT/jobs/originalCrawl/logs/persistlog.txtser.gz"/>
 </bean>
 ```
 
 ### Configurando outros processadores
 
A configuração acima garante que as informações históricas de busca sejam retidas entre os rastreamentos. Se isso afeta a obtenção ou armazenamento de URIs depende das configurações em outros processadores, o mais importante é o processador FetchHTTP e o WARCWriterProcessor ou ARCWriterProcessor.

No processador FetchHTTP, as propriedades sendIfNoneMatch e sendIfModifiedSince controlam se os headers HTTP If-None-Match ou If-Modified-Since são enviados em uma solicitação se as informações do histórico de URI (data de obtenção prévia ou informações etag) para suportá-los estiverem presentes.

Note que ao enviar estes headers, o motor de rastreio pode receber uma resposta '304-Not Modified' do servidor, sem corpo de conteúdo. Dessa maneira, outros URIs podem não ser descobertos e os caminhos seguidos pelo rastreamento original não são reconsiderados para a recriação. Assim, se você deixar *sendIfNoneMatch* e *sendIfModifiedSince* definidos como valores 'true' padrão em um rastreamento ativado para histórico de deduplicação, poderá usar outra técnica para garantir que todos os URIs dos rastreamentos anteriores sejam reconsiderados (como fornecer todos eles ao rastreador já no começo).

Tanto o ARCWriterProcessor quanto o WARCWriterProcessor possuem uma propriedade *skipIdenticalDigests* que, por padrão, é "false". Se marcada como "true", qualquer busca cujo conteúdo tenha um valor digest idêntico ao da busca anterior será ignorada completamente para fins de gravação. Para o ARCWriterProcessor, isso significa que o registro ARC não será gravado e nada mais será gravado em seu lugar. Da mesma forma para o WARCWriterProcessor, nenhum registro de qualquer tipo será gravado. Apenas o crawl.log registrará que a busca ocorreu.

O WARCWriterProcessor possui propriedades adicionais para manipulação de duplicação mais sofisticada. A propriedade *writeRevisitForIdenticalDigests*, por padrão "true", gravará um registro WARC 'revisit' de tamanho reduzido (sem corpo de conteúdo) em vez de um registro de 'revisit' quando um resumo de repetição for detectado consultando as informações do histórico. (Observe que, se o skipIdenticalDigests acima for marcado como "true", nem mesmo isso será gravado - já que toda a escrita já foi ignorada.) Se "false", um registro "response" completo (com todo o conteúdo duplicado) ainda será gravado, mesmo com um valor digest repetido.

A propriedade *writeRevisitForNotModified*, padrão "true", gravará um registro WARC 'revisit' em vez de um registro 'response' quando as respostas '304-Not Modified' forem recebidas. Se "false", um registro normal de 'response' é gravado.

### Avançado: usando um ambiente BDB de histórico não padrão

Os rastreamentos podem desejar armazenar informações de histórico URI persistente em um local diferente do diretório do ambiente BDB padrão ('state'). Motivações para tal escolha podem incluir:

1. espalhando o disco de rastreamento IO para outro lugar
2. manter informações do histórico distintas de outras informações de rastreamento mais transitórias (filas, estado do rastreamento) para facilitar a análise ou a migração separadamente

Para fazer isso, a primeira etapa é definir mais de um BdbModule na configuração de rastreamento e dar a este segundo BdbModule um nome de bean e um diretório de ambiente distintos. Ele também deve ser excluído da consideração da fiação automática do Spring (de modo que apenas um único BdbModule atenda a esse propósito). Por exemplo:

```
<bean id="historyBdb"
  class="org.archive.bdb.BdbModule" autowire-candidate="false">
  <property name="dir" value="history" />
 </bean>
```

Depois, faça com que o outro PersistLoadProcessor e/ou PersistStoreProcessor se refira explicitamente a este BdbModule por nome, em vez de simplesmente aceitar o padrão autowired. Por exemplo:

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
  <property name="bdbModule">
    <ref bean="historyBdb"/>
  </property>
</bean>
<bean id="persistStoreProcessor" class="org.archive.modules.recrawl.PersistStoreProcessor">
 <property name="bdbModule">
    <ref bean="historyBdb"/>
  </property>
</bean>
```

Você também pode carregar históricos passados de um ambiente diferente do qual as novas informações são armazenadas se desejar manter cada ciclo distinto.

### Considerações ao projetar um rastreamento usando redução de duplicação

A redução de duplicação em seu nível atual oferece dois grandes potenciais de benefício ao repetir um rastreamento: economia de largura de banda e economia de espaço de armazenamento.

Para que haja economia de largura de banda, as opções *sendIfNoneMatch* e/ou *sendIfModifiedSince* devem ser ativadas. Em seguida, os servidores podem informar que não houve alterações significativas desde a versão obtida anteriormente - economizando o tempo e o gasto de largura de banda do envio de conteúdo redundante.

No entanto, isso altera as informações disponíveis para o rastreamento de acompanhamento. Ele não verá mais o conteúdo e, portanto, não descobrirá mais os links dessa página, a serem reconsiderados por conta própria. Portanto, se o único caminho para uma página alterada for por meio de uma página inalterada que, devido a uma resposta '304-Not Modified' , nunca é extraída por link no rastreamento de acompanhamento, a página alterada nunca será considerada para rastreamento e, portanto, a mudança não será descoberta nem arquivada. Assim, é importante que, ao usar esses recursos condicionais-GET de redução de largura de banda, o operador garanta manualmente que todos os URIs de interesse do primeiro rastreamento sejam enfileirados no rastreamento de acompanhamento (seja como seeds ou alguma outra adição durante o rastreamento). Para que haja economia de largura de banda, as opções *sendIfNoneMatch* e/ou *sendIfModifiedSince* devem ser ativadas. Em seguida, os servidores podem informar que não houve alterações significativas desde a versão obtida anteriormente - economizando o tempo e o gasto de largura de banda do envio de conteúdo redundante.

Devido a essa complicação, muitos rastreamentos podem querer usar apenas a redução de duplicação baseada em digest. Isso não oferece economia de largura de banda: todos os URIs são buscados novamente. Mas, quando o digest principal do conteúdo não é alterado, o conteúdo ou não é reescrito ou apenas reescrito de forma abreviada (como um registro WARC 'revisit'). Essa abordagem envolve a configuração das propriedades *sendIfModifiedSince* e *sendIfNoneMatch* do FetchHTTP como "false".

Um aprimoramento futuro para esses recursos pode levar adiante as informações de outlink descobertas como parte do histórico do URI, de modo que os outlinks possam ser 'virtualmente descobertos' mesmo quando '304-Not Modified' significa que nenhuma cópia extraível do conteúdo inalterado está disponível para o rastreador.

## Scripts de utilidade Unix

O Heritrix possui scripts de utilidade Unix.

manifest_bundle.pl

Esse script agrupará todos os recursos mencionados no arquivo de manifesto de rastreamento. Um pacote é uma tar ball não comprimida ou comprimida. A estrutura de diretórios da tar ball é:

* Diretório de nível superior (nome do rastreamento)
* Três subdiretórios padrões
* Outros subdiretórios arbitrários
* Uso de script

```
manifest_bundle.pl crawl_name manifest_file -f output_tar_file -z [ -flag directory]
-f output tar file. If omitted output to stdout.
-z compress tar file with gzip.
-flag is any upper case letter. Default values C, L, and are R are set to
configuration, logs and reports
```

* manifest-bundle.pl (exemplo)

```
manifest_bundle.pl testcrawl crawl-manifest.txt -f /0/testcrawl/manifest-bundle.tar.gz -z -F filters
```

Para o exemplo acima, a tar ball conterá a seguinte estrutura de diretórios:
| - testcrawl

       |- configurations

       |- logs

       |- reports

       |- filters
       
hoppath.pl

Esse script Perl, localizado em (HERETRIX_HOME)/bin, recria o caminho do salto para o URI especificado. O caminho do salto é o caminho dos links (URIs) que foram seguidos para chegar ao URI especificado.

Uso de script

```
hoppath.pl crawl.log URI_PREFIX
crawl.log Full-path to Heritrix crawl.log instance.
URI_PREFIX URI we're querying about. Must begin 'http(s)://' or 'dns:'.
Wrap this parameter in quotes to avoid shell interpretation
of any '&' present in URI_PREFIX.
```

hoppath.pl (exemplo)

```
hoppath.pl crawl.log 'http://www.house.gov/'
```

hoppath.pl (resultado)

```
2004-02-25-02-36-06 - http://www.house.gov/house/MemberWWW_by_State.html
2004-02-25-02-36-06 L http://wwws.house.gov/search97cgi/s97_cgi
2004-02-25-03-30-38 L http://www.house.gov/
```

O `L` no exemplo refere-se ao tipo de link seguido.

RecoveryLogMapper

A classe Java `org.archive.crawler.util.RecoveryLogMapper` é semelhante ao script `hoppath.pl`. Foi contribuído por Mike Schwartz. O `RecoveryLogMapper` analisa um arquivo de log de recuperação do Heritrix e cria mapas que permitem que um chamador pesquise qualquer URI de origem. O RecoveryLogMapper então retorna uma lista de todos os URIs rastreados com sucesso a partir do seed. O `RecoveryLogMapper` também pode encontrar o URI de origem do qual qualquer URI rastreado foi capturado.

## Guia rápido para criar um perfil

Perfis podem ser criados a partir da página de tarefa ou da página de perfil do Heritrix. Essas páginas exibem os detalhes de uma tarefa ou um perfil, respectivamente. Para criar um novo perfil, escolha a tarefa ou o perfil que o novo perfil será baseado. Clique na tarefa ou no perfil na página do console principal. A página de tarefa/perfil será exibida. Na parte inferior da página, insira o nome do novo perfil na caixa de texto "Copy job to" ou "Copy profile to". Selecione a caixa de seleção "as profile" e clique em "copy". Um novo perfil será criado. Neste ponto, você pode configurar o perfil da mesma maneira que uma tarefa é configurada, editando o arquivo crawler-beans.cxml.

Observação

* Mudanças feitas na tarefa ou perfil base não modificam o novo perfil criado.

## Página de tarefa (Job Page)

O Heritrix 3.0/3.1 introduz a capacidade de executar várias tarefas simultaneamente na mesma instância do rastreador. No Heritrix 1.x, apenas uma tarefa poderia ser executado por vez, enquanto as outras tarefas eram enfileirados atrás da tarefa em execução. O único limite que afeta o número de tarefas que podem ser executadas simultaneamente no Heritrix 3.0/3.1 é a quantidade de memória alocada para o heap Java. Se muitos rastreamentos forem executados, o heap Java poderá, em algum momento, estar esgotado. Isso resultará em um erro OutOfMemory que cancelará os rastreamentos em execução.

Depois que uma tarefa de rastreamento tiver sido criada e configurada corretamente, poderá ser executada. Para iniciar um rastreamento, o usuário deve ir para a página de tarefa clicando na tarefa específica na IUW.

### Elementos de dados da página de tarefa

Job name
O nome da tarefa e o número de vezes que foi executada.

Avaible checkpoints to recover
A partir da versão 3.1, se um rastreamento tiver sido verificado, uma caixa suspensa será exibida mostrando todos os pontos de verificação que foram executados.

Job Log
O log da tarefa contém um registro dos comandos emitidos na página da tarefa, incluindo respostas de comando.

Job Status
Exibe o status da tarefa.

Totals
Exibe estatísticas que fornecem informações sobre o número de documentos rastreados e baixados.

Alerts
Lista os alertas gerados pelo rastreamento. Os alertas podem ser avisos ou problemas que abortam o rastreamento mas que não prejudicam o rastreamento, mas podem prejudicar o resultado do rastreamento.

Rates
Exibe estatísticas que fornecem as taxas nas quais os documentos e bytes de dados estão sendo baixados. O número de URIs processados por segundo, com sucesso, é mostrado. Para esta estatística, é mostrada a taxa do último intervalo de amostragem e a taxa média (entre parênteses). O intervalo de amostragem é geralmente de cerca de 20 segundos e pode ser controlado pela propriedade intervalSeconds do bean Spring do StatisticsTracker. A taxa mais recente de progresso pode flutuar consideravelmente, à medida que a carga de trabalho do rastreador varia e ocorrem a memória de manutenção e as operações de arquivo. Isso é principalmente verdadeiro se o intervalo de amostragem tiver sido definido com um valor baixo. A taxa de coleta de conteúdo bem-sucedida em KB /segundo para o intervalo de amostragem mais recente e (entre parênteses) a média desde o início do rastreamento também são exibidas.

Load
Exibe estatísticas que fornecem informações de carga. O número de encadeamentos ativos, comparado ao total de encadeamentos disponíveis, é mostrado. Normalmente, se apenas um pequeno número de threads estiver ativo, é porque ativar mais threads excederia as configurações de cortesia configuradas. Por exemplo, se todos os URIs restantes estiverem em um único host, apenas um encadeamento estará ativo, a menos que as filas paralelas estejam ativadas. Às vezes, nenhum tópico estará ativo devido a pausas para considerações de cortesia.

Congestion Ratio
O índice de congestionamento é uma estimativa aproximada de quanta capacidade inicial, como um múltiplo da capacidade atual, seria necessária para rastrear a carga de tarefa atual na taxa máxima disponível em determinadas configurações de cortesia. Esse valor é calculado comparando o número de filas internas que estão progredindo em relação àquelas que estão aguardando a disponibilização de um encadeamento.

Deepest Queue
A estatística de fila mais profunda é a cadeia mais longa de URIs que deve ser processada sequencialmente. Essa estatística é um indicador superior do trabalho restante do que o número total de URIs pendentes. Por exemplo, 1000 URIs em 1000 filas podem ser concluídos rapidamente, mas 1000 URIs em uma única fila levarão muito mais tempo para serem concluídos. A profundidade média é a profundidade média do último URI em todas as filas seqüenciais ativas.

Elapsed
Exibe o tempo decorrido, em milissegundos, que uma tarefa foi executada, excluindo o tempo no estado "pausado".

Threads
Esta área da página do trabalho exibe o número de encadeamentos sendo usados. Clicar em "threads" para ver um relatório de detalhado.

Frontier
Exibe estatísticas do Frontier, como o número de URIs enfileirados. Clicar em "frontier" para ver um relatório de detalhado.

Memory
Exibe a quantidade de memória alocada para o heap Java, a quantidade de memória em uso e o tamanho máximo do heap Java.

Crawl Log
Exibe a saída do log de rastreamento. O log de rastreamento contém informações detalhadas sobre um rastreamento em execução, como os URIs que foram buscados.

Advanced
Fornece acesso a recursos avançados que podem ser usados para controlar uma tarefa.

Configuration-referenced Paths
Exibe os caminhos relevantes para a configuração e registro de tarefa. Por exemplo, o caminho para o arquivo `crawl.log` é exibido.

### Operações da página de trabalho

Edit
Permite editar o arquivo `crawler-beans.cxml`. O arquivo `crawler-beans.cxml` contém a configuração Spring da tarefa de rastreamento. A edição desse arquivo é a maneira padrão de configurar uma tarefa ou perfil.

Build
Cria as classes Java Spring configuradas por meio do arquivo `crawler-beans.cxml`. Antes de uma tarefa ser executada, ela deve ser construída.

Launch
Inicia uma tarefa de rastreamento. Antes de ser lançada, a tarefa deve ser construída. Uma vez que a tarefa é lançada, ela estará pausada ou em execução. Se estiver pausada, o botão "unpause" deve ser clicado para iniciar o rastreamento. A partir da versão 3.1, se um ponto de verificação ou vários pontos de verificação foi/foram executados, um ponto de verificação pode ser selecionado na caixa suspensa do ponto de verificação. A tarefa pode ser reiniciada no ponto de verificação clicando em "launch".

Pause
Pausa o rastramento em andamento.

Unpause
Despausa o rastreamento.

Checkpoint
Salva o estado atual do rastreamento no armazenamento. Durante o tempo em que o rastreamento está sendo verificado, ele é pausado e nenhum URI será rastreado. O ponto de verificação (checkpoint) é útil se um rastreamento deve ser interrompido e, em seguida, reiniciado.

Terminate
Encerra um rastreamento.

Teardown
Descarta as classes Spring Java atuais da tarefa e permite que uma nova configuração Spring seja construída. Qualquer alteração no arquivo `crawler-beans.cxml` após o botão "Build" ter sido chamado requer uma desmontagem (teardown) e outra montagem a ser executada.

Copy
Permite a cópia da configuração da tarefa atual para uma nova tarefa ou perfil.

Scripting Console
Exibe um formulário de entrada que pode ser usado para inserir e executar comandos de script. Os comandos de script podem ser usados para controlar o comportamento de uma tarefa de rastreamento. Várias linguagens de script estão disponíveis, como AppleScript e ECMAScript. Exemplos de scripts podem ser encontrados aqui.


Browse Beans
Exibe a hierarquia dos beans Spring que formam uma tarefa de rastreamento. As propriedades e associações de cada bean podem ser visualizadas ou editadas clicando no bean.

## Frontier

O Frontier é um bean Spring que mantém o estado interno do rastreamento. O estado do rastreamento contém informações como URIs rastreadas ou descobertas anteriormente, bem como outras informações relevantes para o status do rastreamento.

Há apenas um bean Frontier por tarefa de rastreamento.

Crucialmente, a frontier do Heritrix3, além de armazenar várias filas de URLs para rastreamento em ordem de prioridade, também controla as configurações de cortesia de atraso de rastreamento por filas. Ou seja, controla, também, quando CrawlURIs devem ser rastreados, não somente a prioridade de rastreamento.

O Heritrix BdbFrontier também implementa a rotação de filas, para garantir que todas as filas sejam vistas, mesmo quando houver um número maior de filas do que de threads disponíveis para executar o rastreamento. Isso significa que as filas de rastreamento do Heritrix têm "orçamentos de sessão" (para manipular a rotação), além de cotas de rastreamento gerais (que são aplicadas a todo o rastreamento).

Nas versões 3.0 e 3.1, existe apenas um tipo de Frontier, o Heritrix BdbFrontier. Outros frontiers que foram incluídos no Heritrix 1.x não são mais suportados.

Para mais detalhes, ver:

* Configurações do Frontier 
* Frontier queue budgets
* Heritrix BdbFrontier (detalhes técnicos)

### Configurações do Frontier

Politeness

Uma combinação de várias configurações controla a politeness do Frontier. É importante observar que, a qualquer momento, apenas um URI de qualquer host é processado. As regras de cortesia a seguir impõem um tempo de espera adicional entre o final do processamento de um URI e o início do próximo.

* delayFactor - Essa configuração impõe um atraso entre a obtenção de URIs do mesmo host. O atraso é um múltiplo do tempo necessário para buscar o último URI baixado do host. Por exemplo, se foram necessários 800 milissegundos para buscar o último URI de um host e o delayFactor for 5 (um valor muito alto), o Frontier aguardará 4000 milissegundos (4 segundos) antes de permitir que outro URI desse host seja processado.

* maxDelayMs- Essa configuração impõe um limite máximo no tempo de espera criado pelo delayFactor. Se definido como 1000 milissegundos, o atraso máximo entre as buscas de URI do mesmo host nunca excederá esse valor.

* minDelayMs- Essa configuração impõe um limite mínimo de politeness. Tem precedência sobre o valor calculado pelo delayFactor. Por exemplo, o valor de minDelayMs pode ser definido como 100 milissegundos. Se o delayFactor gerar uma espera de apenas 20 milissegundos, o valor de minDelayMs irá substituí-lo e a busca de URI será atrasada por 100 milissegundos.

```
<bean id="disposition" class="org.archive.crawler.postprocessor.DispositionProcessor">
<property name="delayFactor" value="5.0" />
<property name="maxDelayMs" value="30000" />
<property name="minDelayMs" value="3000" /></bean>
```

Política de tentativas

O Frontier pode ser usado para limitar o número de tentativas de busca para um URI. O Heritrix tentará recuperar um URI porque o erro de busca inicial pode ser uma condição transitória.

* maxRetries - Essa configuração limita o número de novas tentativas de busca em um URI devido a erros transitórios.

* retryDelaySeconds - Essa configuração determina o tempo do período de espera entre novas tentativas.

```
<bean id="frontier"
   class="org.archive.crawler.frontier.BdbFrontier">

  <property name="retryDelaySeconds" value="900" />
  <property name="maxRetries" value="30" />

 </bean>
 ```
 
 Limites de Largura de Banda
 
O Frontier permite ao usuário limitar o uso de largura de banda, retendo URIs quando o uso da largura de banda excedeu certos limites. Como as limitações de uso da largura de banda são calculadas ao longo de um período de tempo, ainda pode haver picos de uso que excedam os limites.

* maxPerHostBandwidthUsageKbSec - Essa configuração limita a largura de banda máxima a ser usada por qualquer host. Essa configuração limita a carga colocada pelo Heritrix no host. É, portanto, uma configuração de politeness.

```
<bean id="disposition" class="org.archive.crawler.postprocessor.DispositionProcessor">
<property name="maxPerHostBandwidthUsageKbSec" value="500" />
</bean>
```

Parâmetros de extração

A partir da versão 3.1, o comportamento do Frontier em relação à extração de links pode ser controlado pelos seguintes parâmetros.

* extract404s - Essa configuração permite que o operador evite a extração de links de páginas 404 (Not Found). O configuração padrão é "true", o que mantém o comportamento pré-3.1 de extração links de páginas 404.

```
<bean id="frontier" class="org.archive.crawler.frontier.BdbFrontier">
<property name="extract404s" value="true" />
</bean>
```

* extractIndependently - Essa configuração incentiva os processadores de extração a sempre executar a extração da melhor forma possível, mesmo que um extrator anterior tenha marcado um URI como já manipulado. Defina o valor como "true" para ativar essa configuração. O definição padrão é "false", o que mantém o comportamento pré-3.1.

### Heritrix BdbFrontier

O BdbFrontier visita URIs e descobre sites de uma maneira geralmente ampla. Oferece opções de configuração para controlar como ele controla a atividade em determinados hosts. Algumas configurações permitem que haja uma tendência a finalizar os hosts em andamento (rastreamento "site-first") ou que haja alternações entre todos os hosts com URIs pendentes.

Os URIs descobertos são rastreados apenas uma vez, com exceção às informações do robots.txt e DNS, que podem ser configuradas para serem atualizadas em intervalos específicos para cada host.

A partir da versão 3.1, há duas novas propriedades.

| Propriedade  | Padrão | Descrição |
| ------------- | ------------- | | ------------- | 
| largestQueuesCount| 20 | Controla quantas, das filas maiores, são rastreadas e relatadas no "relatório frontier". |
| maxQueuesPerReportCategory | 2000  | Controla o número máximo de filas por categoria listadas no "relatório frontier". |

Quando as filas diminium do top-N ou o valor é alterado no meio do rastreamento, as informações sobre as filas maiores pode não ser exata. A lista é atualizada apenas quando uma fila passa para o grupo maior.

### Detalhes da implementação

O armazenamento da fila é gerenciado por meio de um banco de dados BDB JE incorporado. É um armazenamento simples de valor-chave, portanto, a multiplicidade de filas é implementada como prefixos-chave. Cada CrawlURI é armazenado no banco de dados BDB como um binary blob serializado usando Kryo, sob uma chave que combina o prefixo de fila (classKey) e a prioridade de rastreamento do CrawlURI.

A lista de active/snoozed/etc são mantidas na memória e gravadas no disco durante o checkpoint no formato JSON. Se você continuar a partir do checkpoint, o BdbFrontier será reutilizado, mas as informações necessárias da fila serão fornecidas pelos arquivos JSON. Se o banco de dados do frontier *não* for reutilizado a partir de um ponto de verificação, o banco de dados será 'truncado' e todos os dados no BdbFrontier serão descartados.

A atualização do conteúdo do frontier a partir de vários encadeamentos exige cuidado, pois é necessário fazer alterações e confirmá-las no disco sem que ocorram conflitos. Uma vez que qualquer alteração tenha sido feita, por exemplo, um WorkQueue, a chamada `wq.makeDirty ()` é usada para iniciar um processo no qual o WorkQueue é serializado para o disco e lido novamente (para assegurar a consistência, mas descartando todos os campos temporários). Isso significa que as atualizações para cada WorkQueue devem ser sincronizadas nos encadeamentos para que não haja duas atualizações ao mesmo tempo. Por exemplo:

```
 synchronized (wq) {
      ...do updates...
      wq.makeDirty();
    }
```

Tudo isso é feito usando um ObjectIdentityBdbManualCache, que torna possível interagir com o banco de dados como uma coleção simples, enquanto mantém na memória apenas o que for necessário.

## Framework Spring

O software Heritrix é baseado no framework Spring para Java. O framework Spring define uma construção chamada bean. Um bean é um componente configurável que consiste em propriedades (como o endereço de email do operador do Heritrix) e referências a outros beans. A configuração do Heritrix é realizada configurando beans. Um bean é representado como um elemento XML nos arquivos de configuração do Spring. Um exemplo de um bean é mostrado abaixo.

**fetchProcessors Spring Bean

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

Esse bean consiste em um identificador exclusivo (fetchProcessors) e uma lista de propriedades que são referências a outros beans. Um exemplo de alteração de configuração seria colocar o bean extrator XML à frente do bean extrator HTTP para que o conteúdo, como feeds RSS, possa ser rastreado. O bean fetchProcessors após essa alteração de configuração é mostrado abaixo.

**fetchProcessors Bean com extractorXML

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from XML... -->
<ref bean="extractorXML"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

## Diretório "action"

Cada diretório de tarefa contém um diretório "action". Colocando arquivos nesse diretório, você pode acionar ações em uma tarefa de rastreamento em andamento, como a adição de novos URIs ao rastreamento.

Em intervalos regulares (por padrão, menos de um minuto), o rastreamento observará novos arquivos nesse diretório e executará ações com base no sufixo do nome do arquivo e no conteúdo. Quando a ação for concluída, o arquivo será movido para o diretório 'done' mais próximo. (Por esse motivo, os arquivos devem ser compostos fora do diretório "action" e movidos para lá como um todo atômico. Caso contrário, um arquivo pode ser processado e movido enquanto ainda está sendo composto.)

Os seguintes sufixos de arquivos são suportados:

| Sufixo  | Descrição |  
| ------------- | ------------- |  
| `.seeds`| Um arquivo `.seeds` deve conter seeds que o operador do Heritrix deseja incluir no rastreamento. Colocar um arquivo `.seeds` no diretório "action" adicionará os seeds ao rastreamento em andamento. As mesmas diretivas que podem ser usadas em listas de seeds durante a configuração inicial de rastreamento podem ser usadas aqui. Se os seeds introduzidos no rastreameno dessa maneira já estiverem no frontier (talvez já um seed), esse método não as força. |
| `.recover` | Um arquivo `.recover` será usado como um diário de recuperação tradicional. (O diário de recuperação pode reproduzir aproximadamente o estado das filas de um rastreamento e o conjunto já incluído, repetindo todos os eventos de conclusão de URI e de descoberta de URI. Um diário de recuperação reproduz menos estados do que um ponto de verificação adequado.) Em uma primeira passagem, todas as linhas que começarem com `Fs` no diário de recuperação serão consideradas incluídas, para que não possam ser enfileiradas novamente. Em seguida, em uma segunda passagem, as linhas que começarem com `F+` serão enfileiradas novamente para rastreamento (se não forem impedidas pela primeira passagem).  | 
| `.include` | Um arquivo `.include` será usado como um diário de recuperação, mas todos os URIs, independentemente do prefixo de linha, serão marcados como já incluídos, evitando que eles sejam enfileirados novamente a partir desse ponto. (Os URIs já enfileirados ainda estarão qualificados para rastreamento quando surgirem.) O uso de um arquivo `.include` é uma maneira de suprimir o novo rastreamento de URIs. |
| `.schedule` | Um arquivo `.schedule` será usado como um diário de recuperação, mas todos os URIs, independentemente do prefixo de linha, serão oferecidos para enfileiramento. (No entanto, se eles forem reconhecidos como já incluídos, eles não serão enfileirados.) O uso de um arquivo `.schedule` é uma maneira de incluir URIs em um rastreamento em andamento, inserindo-os nas filas de rastreamento do Heritrix. |
| `.force` | Um arquivo `.force` será usado como um diário de recuperação com todos os URIs marcados para agendamento forçado. O uso de um arquivo `.force` é uma maneira de garantir que os URIs já incluídos sejam reenquadrados (e, portanto, sejam rastreados novamente).

Qualquer um desses arquivos pode ser gzipado. Qualquer um dos arquivos no formato de diário de recuperação (`.recover`, `.include`, `.schedule`, `.force`) pode ter um `.s` inserido antes do sufixo funcional (por exemplo, `frontier.s.recover.gz`), o que fará com que os URIs sejam testados no escopo antes que qualquer outra inserção ocorra.

Por exemplo, o seguinte arquivo pode ser movido para o diretório "action" para agendar um URL:

**example.schedule

```
F+ http://example.com
```

Para usar o diretório action, o bean `ActionDirectory` deve ser configurado no arquivo `crawler-beans.cxml`, conforme ilustrado abaixo.

```
<bean id="actionDirectory" class="org.archive.crawler.framework.ActionDirectory">
<property name="actionDir" value="action" />
<property name="initialDelaySeconds" value="10" />
<property name="delaySeconds" value="30" />
</bean>
```

A classe org.archive.crawler.frontier.FrontierJournal contém as constantes reconhecidas como diretivas possíveis em um diário de recuperação.

Observe que as linhas 'F +' do formato de diário de recuperação podem incluir um 'hops-path' e 'via URI', que são preservadas quando um URI é enfileirado pelos mecanismos acima, mas isso pode não ser uma representação completa de todo o estado de URI a partir de sua descoberta em um rastreamento normal.

## Glossário

Bytes, KB and statistics

O Heritrix adere às seguintes convenções para exibir quantidades de bytes e bits:

| Código  | Tipo |  
| ------------- | ------------- |  
| B | Bytes |
| KB Kilobytes | 1KB = 1024 B |
| MB Megabytes | 1MB = 1024 KB |
| GB Gigabytes | 1GB = 1024 MB |
| b | bits |
| KB Kilobits | 1Kb = 1000 b |
| Mb Megabits | 1Mb = 1000 kb |
| Gb Gigabits | 1000 Mb |

Isso também se aplica a todos os logs.

Ponto de verificação (Checkpointing)

O ponto de verificação do Heritrix é fortemente influenciado pela verificação do rastreador Mercator. Em um artigo sobre o Mercator, o ponto de verificação é descrito da seguinte maneira: "O ponto de verificação é uma parte importante de qualquer processo de execução longo, como um rastreamento da Web. 'Ponto de verificação' é a gravação de uma representação do estado do rastreador em um armazenamento estável. Caso aconteça uma falha, o ponto de verificação deve ser suficiente para que, ao ser lido pelo rastreador, ele consiga recuperar seu estado antes da falha e retomar o rastreamento a partir desse ponto. Por essa definição, no caso de uma falha, qualquer trabalho realizado depois do ponto de verificação mais recente é perdido, apenas o trabalho realizado antes fica salvo. No Mercator, a frequência com que o encadeamento secundário realiza um ponto de verificação é configurável pelo usuário, geralmente de 1 a 4 vezes por dia."

Ver Ponto de Verificação para informações sobre a implementação do Heritrix.

CrawlURI

Um URI e seus dados associados, como o URI pai e o número de links.

Data e hora

Todos os horários no Heritrix são GMT, assumindo que o relógio e o fuso horário no sistema local estão corretos. Isso significa que todas as datas/horas nos registros são GMT, todas as datas/horas mostradas na IUW são GMT, e todas as horas ou datas inseridas pelo usuário devem estar em GMT.

URIs descobertos 

Um URI descoberto é qualquer URI confirmado dentro do "escopo". Isso inclui os URIs que foram processados, estão sendo processados e terminaram o processamento. Não inclui URIs que foram "esquecidas". Os URIs esquecidos são URIs considerados fora do escopo durante a busca. É provável que isso aconteça pela alteração da definição do escopo pelo operador.

Observação: Como o mesmo URI pode ser buscado várias vezes (pelo menos na maioria dos Frontiers), o número de URIs descobertos pode ser um pouco menor do que os itens combinados enfileirados, em processo e finalizados. Isso ocorre porque os URIs duplicados estão sendo enfileirados e processados. É provável que a variação seja mais alta em Frontiers que estão implementando estratégias "revisit".

Caminho de descoberta (Discovery Path)

Cada URI tem um caminho de descoberta. O caminho contém um caractere para cada link ou incorporado do seed.

A legenda dos caracteres é a seguinte:

* R - Redirect
* E - Embed
* X - Speculative embed (aggressive JavaScript link extraction)
* L - Link
* P - Prerequisite (such as DNS lookup or robots.txt)
* I - A partir da versão 3.1. Não necessariamente no material de origem, mas deduzido por convenção (como /favicon.ico)

O caminho de descoberta de um seed é uma cadeia vazia.

Frontier

Módulo conectável no Heritrix que mantém o estado interno do rastreamento. Ver Frontier.

Host

Um host pode servir vários domínios ou um domínio pode ser servido por vários hosts. Para nossos propósitos, um host é o mesmo que o nome do host em um URI. O DNS não é considerado porque é volátil e pode estar indisponível. Por exemplo, se vários URIs apontarem para o mesmo endereço IP, eles serão considerados três hosts lógicos diferentes (no mesmo nível do protocolo URI/HTTP).

Os proxies HTTP em conformidade se comportam de maneira semelhante. Eles não consideram um URI e um endereço IP intercambiáveis.

Isso não é ideal para politeness porque aplica regras de cortesia ao host físico ao invés do host lógico.

Trabalho de rastreamento 

Para executar um rastreamento, uma configuração deve ser criada. No Heritrix, essa configuração é chamada de tarefa de rastreamento. Uma tarefa de rastreamento é baseada no framework Spring. A tarefa usa beans Spring como objetos de configuração que definem o rastreamento.

Link Hop Count

Número de links, seguidos do seed,  para alcançar um URI. Os seeds têm uma contagem de saltos de links de zero. A contagem de saltos do link é igual à contagem de `Ls` em um caminho de descoberta de URIs.

URIs pendentes

Número de URIs que estão aguardando processamento detalhado. É, também, o número de URIs descobertos que não foram inspecionados quanto ao escopo ou às duplicatas. Dependendo da implementação do Frontier, isso pode sempre ser zero. Também pode ser um número ajustado que considera duplicatas.

Perfil

Um perfil é um modelo base para uma tarefa de rastreamento. Contém todas as configurações em uma tarefa de rastreamento, mas não é considerado "rastreável". O Heritrix não permite que você rastreie diretamente um perfil. Somente tarefas baseados em perfis podem ser rastreados.

Um exemplo comum de uma configuração de perfil é deixar a propriedade `metadata.operatorContactUrl` indefinida para forçar o operador a inserir um valor válido. Isso se aplica ao perfil padrão que acompanha o Heritrix. Outros exemplos seriam deixar a lista de seeds vazia ou não especificar um processador obrigatório.

Os perfis podem ser usados como modelos, deixando suas configurações em um estado inválido. Dessa forma, um operador é forçado a escolher suas configurações ao criar uma tarefa a partir de um perfil. Isso pode ser vantajoso quando um administrador precisa configurar muitas tarefas de rastreamento diferentes para acomodar sua política de rastreamento.

Politeness

Tentativas do software de rastreador de limitar a carga do site que ele está rastreando. Sem restrições politeness, o rastreador pode sobrecarregar sites menores e até mesmo fazer com que sites de tamanhos moderados fiquem devagar. A menos que você tenha permissão expressa para rastrear um site de forma agressiva, você deve aplicar regras rígidas de cortesia a qualquer rastreamento.

Estados de fila

| Estado  | Significado |  
| ------------- | ------------- |
| ready | Filas prontas para emitir uma URL imediatamente. |
| in-process | Filas que emitiram uma URL que está sendo processada no momento. |
| snoozed | Devido ao atraso de rastreamento ou à ao tempo de espera entre tentativas. |
| active | Total de in-proncess + ready + snoozed. |
| inactive | Filas que atualmente não estão sendo consideradas (devido à rotação de filas). |
| inegilible | Filas inativas em que a precedência da fila excede o andar de precedência. |
| retired | Desativadas por algum motivo, ex. essa fila atingiu sua cota alocada. |
| exhausted | Filas que estão vazias. |

URIs enfileirados

O número de URIs enfileirados e aguardando processamento. Os URIs enfileirados incluem todos os URIs que falharam em serem buscados, mas serão tentados novamente.

Expressões regulares

Todas as expressões regulares no Heritrix são expressões regulares do Java.

As expressões regulares de Java diferem daquelas usadas em outras linguagens de programação, como o Perl. Para obter informações detalhadas sobre expressões regulares Java, consulte a descrição do Java API da classe `java.util.regex.Pattern`.

SHA1

Algorítmo (Secure Hash Algorithm (SHA)) usado pelo Heritrix para criptografar arquivos.

Servidor (Server)

Um servidor é um serviço em um host. Pode haver mais de um serviço em um host. Diferentes serviços são geralmente diferenciados pelo número da porta (port number).

Spring

Spring é uma estrutura de aplicativo Java usada pelo Heritrix. As tarefas de rastreamento são baseadas nos componentes Spring, conhecidos como "beans". Para visualizar os beans Spring de uma configuração de rastreamento, use a funcionalidade "Browse Beans".

SURT

SURT significa Sort-friendly URI Reordering Transform. É uma transformação aplicada a URIs que faz com que sua representação da esquerda para a direita corresponda melhor à hierarquia natural dos nomes de domínio.

Um URI <scheme: //domain.tld/path? Query> tem uma forma SURT <scheme://(tld,domain,)/path?query.

A conversão para o formulário SURT também envolve transformar todos os caracteres em minúsculas e a alteração do esquema https para http. Além disso, o caractere "/" após um componente de autoridade de URI só aparecerá no formato SURT se estiver em formato URI simples. Um exemplo de componente de autoridade de URI é a terceira barra em um URI HTTP regular. Essa convenção se mostra importante ao usar URIs reais como uma abreviação de prefixos SURT.

Os URIs de formato SURT não são normalmente usados para especificar URIs exatos para busca. Em vez disso, a forma SURT é útil ao comparar ou classificar URIs. URIs em formato SURT se classificam em grupos naturais. Por exemplo, todos os URIs "archive.org" serão adjacentes, independentemente de subdomínios como "books.archive.org" ou "movies.archive.org".

Mais importante, um URI de formato SURT ou uma versão truncada de um URI de formato SURT pode ser usado como um prefixo SURT. Um prefixo SURT geralmente corresponderá a todos os URIs dentro de uma área comum de interesse. Por exemplo, o prefixo http://(is será compartilhado por todos os URIs no domínio de nível superior `.is`.

Prefixo SURT

Um URI em formato SURT, especialmente se truncado, pode ser útil como um "prefixo SURT", uma cadeia de prefixo compartilhado de todos os URIs de formulário SURT na mesma área de interesse. Por exemplo, o prefixo http://(is. será compartilhado por todos os URIs de formato SURT no domínio de nível superior `.is`. O prefixo http://(org, archive.www,)/movies será compartilhado por todos os URIs em www.archive.org com um caminho que comece com /movies. http://(org,archive.www,)/movies é também um URI de formato SURT completo válido.

Uma coleção de prefixos SURT classificados é uma maneira eficiente de especificar o escopo de rastreamento desejado. Por exemplo, qualquer URI cujo formato SURT comece com qualquer um dos prefixos deve ser incluído no escopo.

Um pequeno conjunto de convenções pode ser usado para calcular um "prefixo SURT implícito" de um URI regular, como um URI fornecido como um seed de rastreamento. Essas convenções são:

1. Converter o URI para seu formato SURT.
2. Se houver pelo menos três barras ("/") no formato SURT, remova todos os caracteres após a última barra. Por exemplo, http://(org,example,www,)/main/subsection/ não é alterado. http://(org,example,www,)/main/subsection é truncado para http://(org,example,www,)/main/. http://(org.example,www,)/ não é alterado e http://(org,example,www) não é alterado.
3. Se o formato final terminar com um parentêses fora (")", remova o parênteses. Cada um dos exemplos acima, exceto o último, permanece inalterado. O último, http://(org,example,www,) , torna-se becomes http://(org,example,www,.

Isso permite que muitos URIs de seeds, em sua forma usual, indiquem os prefixos SURT mais úteis para rastrear URIs relacionados. A presença ou ausência de um "/" à direita em URIs sem informações de caminho adicionais é um indicador sutil se os subdomínios do domínio fornecido devem ser incluídos.

Por exemplo, o seed http://www.archive.org/ se tornará o formato SURT e fornecerá o prefixo SURT http://(org,archive,www,)/ e é o prefixo de todas as URIs de formato SURT em www.archive.org. No entanto, qualquer URI de subdomínio como http://homepages.www.archive.org/directory seria excluído porque seu formato SURT http://homepages.www.archive.org/directory não inicia com o prefixo SURT completo, incluindo o ")" removido do seed.

[Observação: esse parágrafo aplica-se apenas ao H1] Em contraste, o seed http://www.archive.org (note a falta de barra final) se tornará o formato SURT http://(org,archive,www,) e o prefixo SURT implícito http://(org,archive,www, (observe a falta de parênteses à direita). Esse será o prefixo de todos os URIs em www.archive.org, bem como quaisquer URIs de subdomínio como http://homepages.www.archive.org/directory, porque o prefixo SURT completo aparece no formato URI SURT de subdomínio.

Toe Threads

Ao rastrear, o Heritrix emprega um número configurável de Toe Threads para processar URIs. Cada um desses encadeamentos solicitará um URI do Frontier, aplicará o conjunto de Processadores a ele e, finalmente, o reportará como concluído para o Frontier.

## Configurando o escopo do rastreamento usando DecideRules

O escopo de rastreamento define o conjunto de possíveis URIs que podem ser capturados por um rastreamento. Esses URIs são determinados pelos DecideRules, que trabalham em conjunto para limitar ou expandir o conjunto de URIs rastreados. Cada DecideRule, quando apresentado com um objeto (na maioria das vezes um URI de alguma forma), responde com uma das três decisões:

* ACCEPT: O objeto é aceito
* REJECTED: O objeto é rejeitado
* PASS: Nenhuma decisão foi feita, mantém-se a anterior

Um URI sob consideração começa sem status definido. Cada rule é aplicada por vez ao URI candidato. Se o rule decidir ACCEPT ou REJECT, o status do URI será definido de acordo. Depois que todas os rules forem aplicados, o URI será determinado como "no escopo" se seu status for ACCEPT. Se seu status for REJECT, ele será descartado.

Sugerimos começar os rules com nossas configurações padrões recomendadas e realizar pequenos rastreamentos de teste. Com esses testes, tente entender porque certos URIs são determinados ou descartados sob essas configurações. Em seguida, faça pequenas alterações individuais no escopo para obter efeitos desejados que não sejam padrão. Criar um novo conjunto de rules a partir do zero pode ser difícil e pode facilmente resultar em rastreamentos que não podem fazer o progresso mínimo que outras partes do rastreador esperam. Da mesma forma, fazer muitas mudanças de uma só vez pode obscurecer a importância da interação e ordenação dos rules.

### DecideRules

A tabela a seguir lista os DecideRules disponíveis:

| Decide Rule  | Descrição |  
| ------------- | ------------- |
| AcceptDecideRule | Aceita qualquer URI. |
| ContentLengthDecideRule | Aceita URIs se o comprimento do conteúdo for menor que o limite. O limite padrão é 2^63, significando que qualquer documento será aceito. |
| PathologicalPathDecideRule | Rejeita qualquer URI que contenha um número excessivo de segmentos de caminho idênticos e consecutivos. Por exemplo, http://example.com/a/a/a/a/a/foo.html |
| PredicatedDecideRule | Aplica uma decisão configurada somente se um teste for avaliado como "true". |
| ExternalGeoLocationDecideRule | Aceita URIs localizados em um determinado país. |
| FetchStatusDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca igual à configuração "target-status". |
| HasViaDecideRule | Aplica a decisão configurada a qualquer URI que tenha uma "via". Uma via é qualquer URI que seja um sed ou algum tipo de adição feita no meio do rastreamento. |
| HopCrossesAssignmentLevelDomainDecideRule | Aplica a decisão configurada a qualquer URI que seja diferente na parte de seu nome de host/domínio que é atribuído/vendido por registradores. A parte é referida como o "assignment-level-domain" (ALD). |
| IdenticalDigestDecideRule | This DecideRule applies the configured decision to any URI whose prior-history content-digest matches the latest fetch. |
| MatchesListRegexDecideRule | Aplica a decisão configurada a qualquer URI que corresponda às expressões regulares fornecidas. |
| NotMatchesListRegexDecideRule | Aplica a decisão configurada a qualquer URI que não corresponda às expressões regulares fornecidas. |
| MatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que corresponda à expressão regular fornecida. |
| ClassKeyMatchesRegexDecideRule | Aplica a decisão configurada a qualquer chave de classe URI que corresponda à expressão regular fornecida. Uma chave de classe URI é uma cadeia que especifica o nome da fila Frontier na qual uma URI deve ser colocada. |
| ContentTypeMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI cujo tipo de conteúdo esteja presente e corresponda à expressão regular fornecida. |
| ContentTypeNotMatchesRegexDecideRule |Aplica a decisão configurada a qualquer URI cujo tipo de conteúdo não corresponda à expressão regular fornecida. |
| FetchStatusMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca que corresponda à expressão regular fornecida. |
| FetchStatusNotMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca que não corresponda à expressão regular suprida. |
| HopsPathMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI cujo "hops-path" corresponda à expressão regular fornecida. O hops-path é uma string que consiste em caracteres que representam o caminho que foi usado para acessar o URI. Um exemplo de um hops-path é "LLXE". |
| MatchesFilePatternDecideRule | Aplica a decisão configurada a qualquer URI cujo sufixo corresponda à expressão regular fornecida. |
| NotMatchesFilePatternDecideRule | Aplica a decisão configurada a qualquer URI cujo sufixo não corresponda à expressão regular fornecida. |
| NotMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que não corresponda à expressão regular fornecida. |
| NotExceedsDocumentLengthThresholdDecideRule | Aplica a decisão configurada a qualquer URI cujo comprimento de conteúdo não exceda o limite configurado. O tamanho do conteúdo vem do header HTTP ou do tamanho real do conteúdo baixado do URI. A partir da versão 3.1, essa regra foi renomeada para ResourceNoLongerThanDecideRule. |
| ExceedsDocumentLengthThresholdDecideRule | Aplica a decisão configurada a qualquer URI cujo tamanho do conteúdo exceda o limite configurado. O tamanho do conteúdo vem do header HTTP ou do tamanho real do conteúdo baixado do URI. A partir da versão 3.1, essa regra foi renomeada para ResourceLongerThanDecideRule. |
| SurtPrefixedDecideRule | aplica a decisão configurada a qualquer URI (expresso na forma SURT) que começa com um dos prefixos no conjunto configurado. Esse DecideRule retorna true quando o prefixo de um determinado URI corresponde a qualquer um dos SURTs listados. A lista de SURTs pode ser configurada de diferentes maneiras: o parâmetro surtsSourceFile especifica um arquivo para ler a lista de SURTs. Se o parâmetro seedsAsSurtPrefixes estiver definido como true, SurtPrefixedDecideRule adicionará todos os seeds à lista SURTs. Se a propriedade alsoCheckVia estiver configurada como true (padrão false), SurtPrefixedDecideRule também considerará Via URIs na correspondência. A partir da versão 3.1, o parâmetro "surtsSource" pode ser qualquer ReadSource, como um ConfigFile ou um ConfigString. Isso dá ao SurtPrefixedDecideRule a flexibilidade da propriedade "textSource" do bean TextSeedModule. |
| NotSurtPrefixedDecideRule | Aplica a decisão configurada a qualquer URI (expresso na forma SURT) que não comece com um dos prefixos no conjunto configurado. | 
| OnDomainsDecideRule | Aplica a decisão configurada a qualquer URI que esteja em um dos domínios do conjunto configurado. |
| NotOnDomainsDecideRule | Aplica a decisão configurada a qualquer URI que não esteja em um dos domínios do conjunto configurado. |
| OnHostsDecideRule | Aplica a decisão configurada a qualquer URI que esteja em um dos hosts do conjunto configurado. |
| NotOnHostsDecideRule | Aplica a decisão configurada a qualquer URI que não esteja em um dos hosts do conjunto configurado. |
| ScopePlusOneDecideRule | Aplica a decisão configurada a qualquer URI que seja um nível além do escopo configurado. |
| TooManyHopsDecideRule | Rejeita qualquer URI cujo número total de saltos esteja acima do limite configurado. |
| TooManyPathSegmentsDecideRule | Rejeita qualquer URI cujo número total de segmentos de caminho esteja acima do limite configurado. Um segmento de caminho é uma cadeia no URI separado por um caractere "/", sem incluir o primeiro "//". |
| TransclusionDecideRule | aceita qualquer URI cujo path-from-seed termine em pelo menos um salto que não seja navlink. Um salto de navlink é representado por um "L". Além disso, o número de saltos não-navlink no path-from-seed não pode exceder o valor configurado. |
| PrerequisiteAcceptsDecideRule | aceita todos os URIs de "pré-requisito". Os URIs de pré-requisito são aqueles cujo caminho de salto tem um "P" na última posição. |
| RejectDecideRule | Rejeita qualquer URI. |
| ScriptedDecideRule | Aplica a decisão configurada a qualquer URI que passe no teste de rules de um script JSR-223. A fonte do script deve ser uma função de um argumento chamada decisionFor. A função retorna o DecideResult apropriado. Variáveis disponíveis para o script incluem o objeto (o objeto a ser avaliado, como um URI), "self " (a ocorrência ScriptDecideRule), e contexto (ApplicationContext do rastreamento, do qual todos os beans de rastreamento nomeados são alcançáveis). |
| SeedAcceptDecideRule | aceita todos os URIs "seeds" (aqueles para os quais isSeed é "true"). |

DecideRules são configurados pelo bean com o id "scope" sob a propriedade denominada "rules".

### DecideRuleSequence Logging

Ative o log `FINEST` na classe `org.archive.crawler.deciderules.DecideRuleSequence` para observar cada avaliação de URI do DecideRule. Isso pode ser feito no arquivo `logging.properties`.

**logging.properties 

```
org.archive.modules.deciderules.DecideRuleSequence.level = FINEST
```

em conjunto com o argumento da VM -Dsysprop,

```
-Djava.util.logging.config.file=/path/to/heritrix3/dist/src/main/conf/logging.properties
```

### escopo crawler-bean

A seção do bean de escopo do arquivo `crawler-beans.cxml` é reproduzida abaixo.

```
<bean id="scope" class="org.archive.modules.deciderules.DecideRuleSequence">

<property name="rules">

<list>
<!-- Begin by REJECTing all... -->
<bean class="org.archive.modules.deciderules.RejectDecideRule">
    </bean>

<!--
 ...then ACCEPT those within configured/seed-implied SURT prefixes...
-->

<bean class="org.archive.modules.deciderules.surt.SurtPrefixedDecideRule">

<!--
 <property name="seedsAsSurtPrefixes" value="true" />
-->
<!-- <property name="alsoCheckVia" value="true" /> -->
<!-- <property name="surtsSourceFile" value="" /> -->

<!--
 <property name="surtsDumpFile" value="surts.dump" />
-->
</bean>

<!--
 ...but REJECT those more than a configured link-hop-count from start...
-->

<bean class="org.archive.modules.deciderules.TooManyHopsDecideRule">
<!-- <property name="maxHops" value="20" /> -->
</bean>

<!--
 ...but ACCEPT those more than a configured link-hop-count from start...
-->

<bean class="org.archive.modules.deciderules.TransclusionDecideRule">
<!-- <property name="maxTransHops" value="2" /> -->
<!-- <property name="maxSpeculativeHops" value="1" /> -->
</bean>

<!--
 ...but REJECT those from a configurable (initially empty) set of REJECT SURTs...
-->

<bean class="org.archive.modules.deciderules.surt.SurtPrefixedDecideRule">
<property name="decision" value="REJECT"/>
<property name="seedsAsSurtPrefixes" value="false"/>
<property name="surtsDumpFile" value="negative-surts.dump"/>
<!-- <property name="surtsSourceFile" value="" /> -->
</bean>

<!--
 ...and REJECT those from a configurable (initially empty) set of URI regexes...
-->

<bean class="org.archive.modules.deciderules.MatchesListRegexDecideRule">
<!-- <property name="listLogicalOr" value="true" /> -->

<!--
 <property name="regexList">
           <list>
           </list>
          </property>
-->
</bean>

<!--
 ...and REJECT those with suspicious repeating path-segments...
-->

<bean class="org.archive.modules.deciderules.PathologicalPathDecideRule">
<!-- <property name="maxRepetitions" value="2" /> -->
</bean>

<!--
 ...and REJECT those with more than threshold number of path-segments...
-->

<bean class="org.archive.modules.deciderules.TooManyPathSegmentsDecideRule">
<!-- <property name="maxPathDepth" value="20" /> -->
</bean>

<!--
 ...but always ACCEPT those marked as prerequisitee for another URI...
-->
<bean class="org.archive.modules.deciderules.PrerequisiteAcceptDecideRule">
    </bean>
</list>
</property>
</bean>
```

## Suporte Whois

A partir da versão 3.1, é fornecido um fetcher opcional para dados do domínio 'whois'. Um pequeno conjunto de servidores 'whois' bem estabelecidos é pré-configurado. O fetcher usa uma interpretação ad-hoc/intuitive de um URI de esquema 'whois:'.

```
<bean id="fetchWhois" class="org.archive.modules.fetcher.FetchWhois">
<property name="specialQueryTemplates">
<map>
<entry key="whois.verisign-grs.com" value="domain %s" />
<entry key="whois.arin.net" value="z + %s" />
<entry key="whois.denic.de" value="-T dn %s" />
</map>
</property>
</bean>
```

Para configurar um seed whois, insira o seed no seguinte formato: whois://hostname/path (por exemplo,  whois://archive.org.) O whois fetcher tentará resolver cada host que o rastreamento encontra usando o domínio atribuído mais acima e o endereço IP do URL rastreado. Portanto, se você rastrear http://www.archive.org/details/texts, o whois fetcher tentará resolver whois:archive.org  e whois:207.241.224.2.

No momento, a funcionalidade whois é experimental. O bean fetchWhois é comentado no perfil padrão.

## Configurações básicas de tarefa de rastreamento

As configurações de rastreamento são configuradas editando o arquivo `crawler-beans.cxml` de uma tarefa. Cada trabalho tem um `crawler-beans.cxml` que contém a configuração Spring para a tarefa.

### Limites de rastreamento

Além dos limites impostos ao escopo do rastreamento, é possível impor limites arbitrários à duração e à extensão do rastreamento com as seguintes configurações:

* maxBytesDownload - Para o rastreamento depois que um número fixo de bytes tiver sido baixado. Zero significa ilimitado
* maxDocumentDownload - Para o rastreamento depois de baixar um número fixo de documentos. Zero significa ilimitado.
* maxTimeSeconds- Para o rastreamento após um determinado número de segundos. Zero significa ilimitado. Para referência, há 3600 segundos em uma hora e 86400 segundos em um dia.

Para definir esses valores, modifique o bean CrawlLimitEnforcer.

```
<bean id="crawlLimitEnforcer" class="org.archive.crawler.framework.CrawlLimitEnforcer">
<property name="maxBytesDownload" value="100000000" />
<property name="maxDocumentsDownload" value="100" />
<property name="maxTimeSeconds" value="10000" />
</bean>
```

**Observação

* Estes não são limites rígidos. Quando um desses limites for atingido, uma finalização completa da tarefa de rastreamento será acionada. Os URIs já sendo rastreados serão concluídos. Consequentemente, o limite definido será excedido por algum outro valor.

### maxToeThreads

O número máximo de toe threads para executar.

Se estiver executando um rastreamento de domínio menor que 100 hosts, um valor que seja aproximadamente duas vezes o número de hosts deve ser suficiente. Valores maiores que 150-200 raramente valem a pena, a menos que sejam executados em máquinas com recursos excepcionais.

```
<bean id="crawlController" class="org.archive.crawler.framework.CrawlController">
<property name="maxToeThreads" value="50" />
</bean>
```

### metadata.operatorContactUrl

O URI do iniciador de rastreamento. Essa configuração fornece ao administrador de um host rastreado um URI para referência em caso de problemas.

```
<bean id="simpleOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">


<property name="properties">


<value>


   1. This Properties map is specified in the Java 'property list' text format
   2. http://java.sun.com/javase/6/docs/api/java/util/Properties.html#load%28java.io.Reader%29


metadata.operatorContactUrl=http://www.archive.org
metadata.jobName=basic
metadata.description=Basic crawl starting with useful defaults


##..more?..##


</value>
</property>
</bean>
```

### Política Robots Honoring 

1. CLASSIC - Obedece todas as regras robots.txt para o user-agent configurado.
2. IGNORE - Ignora todas as regras robots.txt.
3. CUSTOM - Defer to a custom-robots setting.
4. MOST_FAVORED - Crawl URIs if robots.txt allows any user-agent to crawl it. Rastreia URIs se o robots.txt permitir que qualquer agente do usuário o rastreie.
5. MOST_FAVORED_SET- Requer que um conjunto de user-agent alternativos seja fornecido. Para cada página, se algum agente no conjunto for permitido, a página será rastreada.

```
<bean id="robotsHonoringPolicy" class="org.archive.modules.net.RobotsHonoringPolicy">
  <property name="type" value="CLASSIC"/>
</bean>
```

A escolha de opções 3-5 requer informações adicionais sobre configurações.

A partir da versão 3.1, a política de robots honoring pode ser definida no bean "metadata" usando a propriedade "robotsPolicyName".

```
<bean id="metadata" class="org.archive.modules.CrawlMetadata" autowire="byName">
...
    <property name="robotsPolicyName" value="obey"/>
...
</bean>
```

Valores válidos para "robotsPolicyName":

obey - Obey robots.txt directives
classic - Same as "obey"
ignore - Ignore robots.txt directives

The robots honoring policy can also be set by creating a bean that uses one of the following classes.  The bean must be linked to the "metadata" bean.


| Nome da classe | Desrição |  
| ------------- | ------------- |
| org.archive.modules.net.FirstNamedRobotsPolicy | Use an ordered list of User-Agents.  The first User-Agent in the list is the regularly configured User-Agent.  The other User-Agents in the list are those configured in the candidateUserAgents list.  As soon as a matching set of directives is found, these directives are followed.  If none are found, the wildcard directives are used if they exist. |
| org.archive.modules.net.IgnoreRobotsPolicy | Ignora as diretivas do robots.txt |
| org.archive.modules.net.ObeyRobotsPolicy |Obedece as diretivas do robots.txt |
| org.archive.modules.net.CustomRobotsPolicy | Segue uma política de robots personalizada, em vez das próprias declarações do site |
| org.archive.modules.net.MostFavoredRobotsPolicy | Follow a most-favored robots policy that allows a URI to be crawled if either the conventionally-configured User-Agent, or any number of alternate User-Agents, are allowed. |


O exemplo abaixo mostra o uso da política org.archive.modules.net.IgnoreRobotsPolicy.

``` 
<bean id="ignorePolicy" class="org.archive.modules.net.IgnoreRobotsPolicy">
</bean>


<bean id="metadata" class="org.archive.modules.CrawlMetadata" autowire="byName">

        <property name="robotsPolicyName"> <ref bean="ignorePolicy"/> </property>

</bean>
```

Além disso, a partir da versão 3.1, a análise do robots.txt agora tolera curingas finais nas diretivas Disallow, que é um desvio comum do padrão original. O curinga é equivalente ao mesmo prefixo de caminho sem o caractere curinga final. Além disso, o tratamento das diretivas "Allow" e "Disallow" sobrepostas corresponde ao provável entendimento intuitivo dos webmasters e de outros rastreadores. As diretivas mais específicas/mais longas têm precedência.

## Códigos de status

Códigos de status

Cada URI rastreado recebe um código de status. Este código (ou número) indica o resultado de uma busca de URI no Heritrix.

Códigos que variam de 200 a 599 são códigos de resposta HTTP padrão e informações sobre seus significados estão disponíveis na página da Web do consórcio World Wide Web.

Outros códigos de status do Heritrix estão listados abaixo.

| Código | Significado |  
| ------------- | ------------- |
| 1 | Pesquisa de DNS bem-sucedida |
| 0 | Busca nunca iniciada (talvez protocolo não suportado ou URI ilegal) |
| -1 | Falha na pesquisa de DNS |
| -2 | Conexão HTTP falhou |
| -3 | Conexão HTTP quebrada |
| -4 | Tempo limite de HTTP |
| -5 | Exceção de tempo de execução inesperada. Veja runtime-errors.log. |
| -6 | A pesquisa de domínio de pré-requisito falhou, impedindo a tentativa de busca. (O pré-requisito principal é a pesquisa WHOIS. Se você ver isso, é provável que o domínio não exista mais.) |
| -7 | URI reconhecida como não suportada ou ilegal. |
| -8 | Várias tentativas falharam, limite de novas tentativas foi atingido.
| -50 | Status temporário atribuído a URIs aguardando condições prévias. Aparência em logs pode ser um bug. |
| -60 | Status de falha atribuído a URIs. Eles não puderam ser enfileirados pelo Frontier e podem ser inacessíveis. |
| -61 | A obtenção do pré-requisito robots.txt falhou, impedindo uma tentativa de busca. |
| -62 | Algum outro pré-requisito falhou, impedindo uma tentativa de busca. |
| -63 | Um pré-requisito (de qualquer tipo) não pôde ser agendado, impedindo uma tentativa de busca. |
| -404 | Resposta HTTP vazia, interpretada como um 404. |
| -3000 | Ocorrência uma condição de erro Java grave, como OutOfMemoryError ou StackOverflowError, durante o processamento de URI. |
| -4000 | Detecção "chaff" de armadilhas/conteúdo com valor insignificante aplicado. |
| -4001 | O URI encontra-se muitos link-hops longe da seed.
| -4002 | The URI is too many embed/transitive hops away from the last URI in scope.
| -5000 | O URI está fora do escopo no reexame. Isso só acontece se o escopo mudar durante o rastreamento. |
| -5001 | Bloqueado de buscas por configuração do usuário.
| -5002 | Bloqueado por um processador personalizado, que poderia incluir o mapeador de hash (para rastreamento de vários nós), se ativado. |
| -5002 | Bloqueado por exceder uma cota estabelecida. |
| -5004 | Bloqueado devido a exceder um tempo de execução estabelecido. |
| -6000 | Excluído do Frontier pelo usuário.
| -7000 | Thread de processamento foi morto pelo operador. Isso pode acontecer se um encadeamento for uma condição não responsiva. |
| -9998 | As regras do Robots.txt impediram a busca. |

**Observação:

* Códigos e explicações também estão disponíveis no link "Help" na interface do usuário da web.
* Os códigos de status estão sujeitos a alterações entre as versões do Heritrix. Novos códigos podem ser adicionados para abordar novas áreas de problemas.

Os códigos de status do Heritrix também estão documentados no código-fonte (ou no FishEye para H1 e H3) e no glossário.

## Sheets

Sheets fornecem a capacidade de substituir as configurações padrão por domínio. Sheets são coleções de substituições. Contêm valores alternativos para propriedades de objetos que devem ser aplicados em determinados contextos. O destino é especificado como um caminho de propriedade arbitrariamente longo, que é uma string descrevendo como acessar a propriedade a partir de um beanName em um BeanFactory.

Sheets permitem que as configurações sejam sobrepostas com novos valores aplicados por domínios de nível superior (com, net, org, etc), por domínios de segundo nível (yahoo.com, archive.org, etc.), por subdomínios (crawler.archive. org, tech.groups.yahoo.com, etc.) e caminhos principais do URI (directory.google.com/Top/Computers/, etc.). Não há limite de tamanho do prefixo de domínio/caminho que especifica; a sintaxe de prefixos SURT é usada.

A criação de uma sheet envolve a configuração do arquivo `crawler-beans.cxml`, que contém a configuração Spring de uma tarefa.

Por exemplo, se você tiver permissão explícita para rastrear determinados domínios sem o limite habitual de polite rate-limiting, uma Sheet poderá ser usada para criar uma política de rastreamento less polite associada a alguns desses domínios de destino. A configuração dessa sheet para os domínios example.com e example1.com é mostrada abaixo. Este exemplo permite até 5 solicitações pendentes paralelas de cada vez (em vez do padrão 1) e elimina as pausas comuns entre as solicitações

**Observação importante**: A menos que um site alvo forneça permissão explícita para rastreamento extra-agressivo, os padrões típicos do Heritrix, que limitam o rastreador a não mais de um pedido pendente por vez, com esperas de vários segundos entre solicitações e esperas mais longas quando o site está respondendo mais devagar, é o caminho mais seguro. O rastreamento menos educado pode fazer com que o rastreador seja totalmente bloqueado pelos webmasters. Por fim, mesmo com permissão, certifique-se de que a string do User-Agent de seu rastreador inclui um link para informações válidas de contato do operador de rastreamento, para que você possa ser alertado e corrigir qualquer efeito colateral indesejado. 

```
<!-- SHEETOVERLAYMANAGER: manager of sheets of contextual overlays
Autowired to include any SheetForSurtPrefix or
SheetForDecideRuled beans -->
<bean id="sheetOverlaysManager" autowire="byType"
class="org.archive.crawler.spring.SheetOverlaysManager">
</bean>


<bean class='org.archive.crawler.spring.SurtPrefixesSheetAssociation'>
<property name='surtPrefixes'>
<list>
<value>
http://(com,example,www,)/
</value>
<value>
http://(com,example1,www,)/
</value>
</list>
</property>
<property name='targetSheetNames'>
<list>
<value>lessPolite</value>
</list>
</property>
</bean>


<bean id='lessPolite' class='org.archive.spring.Sheet'>
<property name='map'>
<map>
<entry key='disposition.delayFactor' value='0.0'/>
<entry key='disposition.maxDelayMs' value='0'/>
<entry key='disposition.minDelayMs' value='0'/>
<entry key='queueAssignmentPolicy.parallelQueues' value='5'/>
</map>
</property>
</bean>
```

A sheet nomeada `lessPolite`, no exemplo, define sobreposições para várias propriedades de cortesia. A sheet é associada a domínios adicionando um bean `org.archive.crawler.spring.SurtPrefixesSheetAssociation`. Este bean contém os domínios para os quais as sobreposições serão aplicadas.

A penalidade de desempenho no uso de sobreposições é pequena, uma vez que etapas extras no início do processamento do URI decoram o URI com todas as sobreposições aplicáveis de uma vez só.

### Precedência

* `DecideRuledSheetAssociations` tem precedência sobre `SurtPrefixesSheetAssociations`
* As `DecideRuledSheetAssociations` que aparecem posteriormente em `crawler-beans.cxml` têm precedência sobre as que aparecem mais cedo
* No caso de `SurtPrefixesSheetAssociations` concorrentes, SURTs mais específicos têm precedência sobre os menos específicos

### Rastreamento com várias máquinas

Rastreamentos feitos com várias máquinas podem ser executados usando o hashCrawlMapper para atribuir URIs a máquinas diferentes com base em seus nomes de host.

Primeiro adicione o seguinte bean:

```
<bean id="hashCrawlMapper">
         <property name="enabled" value="true" />
         <property name="localName" value="0" />
         <property name="diversionDir" value="diversions" />
         <property name="checkUri" value="true" />
         <property name="checkOutlinks" value="false"/>
         <property name="rotationDigits" value="10" />

         <!-- Number of crawlers being used in the multi-machine setup-->
         <property name="crawlerCount" value="7" />
 </bean>
 ```
 
 Chame o bean na cadeia do CandidateProcessor:
 
 ```
 <bean id="candidateProcessors">
  <property name="processors">
   <list>
<!-- apply scoping rules to each individual candidate URI... -->
 <ref bean="candidateScoper"/>
<!-- Check URIs for crawler assignment -->
 <ref bean="hashCrawlMapper"/>
 ```
 
Todos os rastreadores receberão a mesma lista inicial de seeds. O único valor que deve ser alterado entre diferentes rastreadores é "localName".

Quando o rastreamento iniciar, o diretório "diversions" começará a ser preenchido com arquivos .divert. Cada arquivo .divert será nomeado $timestamp-$localname_currentmachine-to-$localname_assignedmachine.

Os operadores de rastreamento devem configurar um processo em que os URIs contidos nos arquivos .divert sejam copiados de cada rastreador para seus rastreadores atribuídos e enfileirados no rastreamento ativo. Por exemplo, converter o arquivo .divert no formato esperado pelo diretório de ação como um arquivo .schedule seria suficiente.

## Heritrix3 no Mac OS X

### Usando o JAVA 6

* Baixe o Java para Mac OS X
* Vá para `/Applications/Utilities/Java` e execute o **Java Preferences.app**
* No painel *Java application versions*, arraste o **Java SE 6** para o topo da lista.

### Heritrix3 no Windows


### REST API

Este manual descreve a interface de programação de aplicativos REST (API) do rastreador da Web Heritrix. Heritrix é o rastreador de web do Internet Archive com qualidade de arquivamento extensível, escálavel e de código aberto.. Para mais informações sobre o Heritrix, visite http://crawler.archive.org/.

Este documento destina-se a desenvolvedores de aplicativos e administradores interessados em controlar o rastreador da Web do Heritrix por meio de sua API REST.

Qualquer cliente que suporte HTTPS pode ser usado para invocar a API do Heritrix. Os exemplos neste documento usam o curl da ferramenta de linha de comando, que é normalmente encontrado na maioria dos ambientes unix. Curl está disponível para muitos sistemas, incluindo o Windows.

### Crate New Job

```
POST https://(heritrixhost):8443/engine [action=create]
```

Cria uma nova tarefa de rastreamento. Usa a configuração padrão fornecida pelo perfil profile-defaults.

Parâmetros do formulário:
 
action - deve ser `create` 
createpath - o nome da nova tarefa

Exemplo HTML:

```
curl -v -d "createpath=myjob&action=create" -k -u admin:admin --anyauth --location \
  https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "createpath=myjob&action=create" -k -u admin:admin --anyauth --location \
  -H "Accept: application/xml" https://localhost:8443/engine
```

### Add Job Directory

```
POST https://(heritrixhost):8443/engine [action=add]
```

Adiciona um novo diretório de tarefa à configuração do Heritrix. O diretório deve conter um arquivo de configuração cxml.

Parâmetros do formulário:
 
* ação - deve ser `add`
* addpath - o diretório de tarefa para adicionar

Exemplo HTML:

```
curl -v -d "action=add&addpath=/Users/hstern/job" -k -u admin:admin --anyauth --location https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "action=add&addpath=/Users/hstern/job" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine
```

### Build Job Configuration

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=build]
```

Cria a configuração de tarefa para a tarefa escolhida. Lê um arquivo descritor XML e usa o Spring para construir os objetos Java necessários para executar o rastreamento. Um rastreamento tem que ser criado para poder ser executado.

Parâmetros do formulário:

* action: deve ser `build`

Exemplo HTML:

```
curl -v -d "action=build" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=build" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Launch Job

Inicia uma tarefa de rastreamento. A tarefa pode ser iniciada no estado “paused” ou no estado “unpaused”. Se for iniciado no estado “unpaused”, a tarefa iniciará o rastreamento imediatamene.

Parâmetros do formulário:

* action - deve ser `launch`
* checkpointing - campo opcional: Se fornecido, o Heritrix tentará iniciar a partir de um ponto de verificação. Deve ser o nome de um ponto de verificação (por exemplo, cp00001-20180102121229) ou (a partir da versão 3.3) o valor especial `latest`, que selecionará automaticamente o ponto de verificação mais recente. Se nenhum ponto de verificação for especificado (ou se o último ponto de verificação for solicitado e não houver pontos de verificação válidos), um novo rastreamento será iniciado.

Exemplo HTML:

```
curl -v -d "action=launch" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=launch" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Rescan Job Directory

```
POST https://(heritrixhost):8443/engine [action=rescan]
```

Reexamina o diretório principal de tarefa e retorna uma página HTML contendo todos os nomes de tarefas. Também retorna informações sobre as tarefas, como o local do arquivo de configuração da tarefa e o número de iniciamentos.

Parâmetros do formulário:

* action - deve ser `rescan`

Exemplo HTML:

```
curl -v -d "action=rescan" -k -u admin:admin --anyauth --location https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "action=rescan" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine
```

### Pause Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=pause]
```

Pausa uma tarefa. Não haverá nenhum rastreamento enquanto a tarefa estiver pausada.

Parâmetros de formulário:

* action - deve ser `pause`

Exemplo HTML:

```
curl -v -d "action=pause" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=pause" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Unpause Job

Despausa uma tarefa pausada. O rastreamento será retomado (ou começará, no caso de uma tarefa iniciada no estado "paused"), se possível.

Parâmetros do formulário:

* action - deve ser `unpause`

Exemplo HTML:

```
curl -v -d "action=unpause" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=unpause" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Terminate Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=terminate]
```

Encerra uma tarefa em andamento.

Parâmetros do formulário:

* action - deve ser `terminate`

Exemplo HTML:

```
curl -v -d "action=terminate" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=terminate" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Teardown Job

Remove o código Spring usado para executar a tarefa. Quando uma tarefa é "demolida", ela deve ser reconstruída para ser executada.

Parâmetros de formulário:

* action - deve ser `teardown`

Exemplo HTML:

```
curl -v -d "action=teardown" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=teardown" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Copy Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [copyTo]
```

Copia uma configuração de tarefa existente para uma nova configuração de tarefa. Se a caixa de seleção “as profile” estiver selecionada, a configuração da tarefa será copiada como uma configuração de perfil não executável.

Parâmetros de formulário:

* copyTo - o nome da nova tarefa ou configuração de perfil
* asProfile - se deseja copiar a tarefa como uma configuração executável ou como um perfil não executável. O valor `on` significa que a tarefa será copiada como um perfil. Se omitido, a tarefa será copiada como uma configuração executável.

Exemplo HTML:

```
curl -v -d "copyTo=mycopy&asProfile=on" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "copyTo=mycopy&asProfile=on" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Checkpoint Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=checkpoint]
```

Faz um ponto de verificação da tarefa escolhida. O ponto de verificação grava o estado atual de um rastreamento no sistema de arquivos para que o rastreamento possa ser recuperado caso ocorra uma falha.

Parâmetros de formulário:

* action - must be `checkpoint`

Exemplo HTML:

```
curl -v -d "action=checkpoint" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=checkpoint" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Execute Script in Job

```
POST https://(heritrixhost):8443/engine/job/(jobname)/script
```

Executa um script. O script pode ser escrito como Beanshell, ECMAScript, Groovy ou AppleScript.

Parâmetros do formulário:

* engine - o mecanismo de script a ser usado: `beanshell`, `js`, `groovy` ou `AppleScriptEngine`.
* script - o código de script para executar

Exemplo HTML:

```
curl -v -d "engine=beanshell&script=System.out.println%28%22test%22%29%3B" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob/script
```

Exemplo XML:

```
curl -v -d "engine=beanshell&script=System.out.println%28%22test%22%29%3B" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob/script
```

### Submitting a CXML Job Configuration File

```
PUT https://(heritrixhost):8443/engine/job/(jobname)/jobdir/crawler-beans.cxml
```

Submete o conteúdo de um arquivo CXML para uma tarefa escolhida. Os arquivos CXML são os arquivos de configuração usados para controlar uma tarefa de rastreamento. Cada tarefa tem um único arquivo CXML.

Exemplo:

```
curl -v -T my-crawler-beans.cxml -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob/jobdir/crawler-beans.cxml
```

Códigos de status: * 200 OK - Com sucesso, o Heritrix REST API retornará um HTTP 200 sem corpo.

### Convenções e Pressupostos

Os seguintes parâmetros *curl* são usados ao chamar a API.

| Parâmetros *curl* | Descrição |  
| ------------- | ------------- |
| -v | Verbose. Output a detailed account of the curl command to standard out. |
| -d | Dados. Estes são os pares de nome/valor que são enviados no corpo de um POST. |
| -k | Permite conexões a sites SSL sem certificados. |
| -u | Usuário. Permite o envio de um nome de usuário e senha para autenticar a solicitação HTTP. |
| –anyauth | Qualquer tipo de autenticação. Permite a autenticação de uma solicitação com base em qualquer tipo de método de autenticação. | 
| –location | Segue redirecionamentos HTTP. Essa opção é usada para que as chamadas de API que retornam dados (como HTML) não sejam interrompidas após o recebimento de um código de redirecionamento (como um HTTP 303). |
| -H | Defina o valor de um header HTTP. Por exemplo, “Accept: application/xml”.

Supõe-se que o leitor tenha um conhecimento prático do protocolo HTTP e da funcionalidade do Heritrix. Além disso, os exemplos assumem que o Heritrix é executado com um nome de usuário administrativo e senha de "admin".

### Sobre a implementação do REST

Representational State Transfer (REST) é uma arquitetura de software para sistemas hipermídia distribuídos, como a World Wide Web (WWW). O REST é construído sobre o conceito de representações de recursos. Recursos podem ser qualquer conceito coerente e significativo que possa ser abordado. Um URI é um exemplo de um recurso. A representação do recurso é tipicamente um documento que captura o estado atual ou pretendido do recurso. Um exemplo de representação de um recurso é uma página HTML.

O Heritrix usa o REST para expor sua funcionalidade. A implementação do REST usada pelo Heritrix é Restlet. Restlet implementa os conceitos definidos pelo REST, incluindo recursos e representações. Também fornece um container REST que processa solicitações RESTful. O container é o Noelios Restlet Engine. Para informações detalhadas sobre o Restlet, visite http://www.restlet.org/.

O Heritrix expõe sua funcionalidade REST por meio de HTTPS. O protocolo HTTPS é usado para enviar solicitações para recuperar ou modificar configurações de definições e gerenciar tarefas de rastreamento.

## Base de Conhecimento

### Rastreamento responsável

Um rastreamento responsável é um rastreamento que segue as leis e as convenções estabelecidas do rastreamento da web, afim de minimizar os custos que o rastreamento impõe aos sites coletados.

Principais práticas:

* respeitar o robots.txt, a não ser que você tenha recebido permissão explícita para fazer o contrário
* fornecer informações de contato em seu User-Agent e responder prontamente quando for contatado
* usar politeness-delay e outras configurações que afetam a frequência de acessos a um único site para garantir que a maior parte da capacidade de veiculação do site permaneça disponível para outros visitantes
* monitorar regularmente os logs de rastreamento em busca de evidências de caminhos improdutivos/intermináveis (armadilhas) e ajustar ativamente o rastreador para interromper essa atividade quando observado

### Adicionar URIs no meio do rastreamento

(Procedimentos para o Heritrix 1.14.x, procedimentos para H2/H3 irão variar.)

**Via IUW do operador**

Há duas maneiras principais de adicionar URIs a um rastreamento que já foi iniciado a partir da interface do usuário da Web: alterando o arquivo fonte do seed (seeds.txt) e acionando uma nova verificação ou usando a opção 'import URIs' da página 'View/Edit Frontier' (disponível apenas em rastreamentos pausados).

**Edições de seeds**

O arquivo seeds é, por padrão, relido depois de qualquer alteração de configuração (mesmo pequenas alterações não relacionadas); isso pode ser alterado através da configuração "reler-seeds-on-reconfig" do escopo.

(Você pode editar arquivos seeds menores na área de texto na parte inferior de todas as outras configurações editáveis. Arquivos grandes são difíceis ou impossíveis de editar através da área de texto da Web; nesse caso, você ainda pode editar o arquivo no disco por outros métodos.)

Uma nova verificação de arquivos seeds aciona uma tentativa de reagendamento de todos os URIs contidos. No entanto, URIs que já foram agendados não serão reprogramados. Assim, somente os URIs novos no arquivo seed serão agendados recentemente. (E se, por algum motivo, esses novos URIs já estiverem programados por algum outro *discovery-path*, eles não serão reagendados.)

Os escopos que são definidos em termos de seeds também são, por padrão, reconstruídos a partir de seeds em alterações de configurações. (Portanto, se estiver usando esse escopo, o recomendável é não remover as sementes originais, mesmo que elas já estejam agendadas - caso contrário, seu escopo pode ser redefinido para excluir os sites correspondentes.)

**Importar URIs**

Quando o rastreador é pausado, um link "View or Edit Frontier" aparecerá no console. A página correspondente oferece um formulário 'import URIs'.

Este formulário espera um caminho de arquivo local para o rastreador; o arquivo pode estar em qualquer um dos três formatos listados (um URI por linha, um crawl.log ou um log de recuperação descompactado). Se o formato fornecido incluir informações de 'hops-path' e 'via', os URIs importados compartilharão essas informações. Se a caixa 'force revisit' estiver marcada, os URIs fornecidos serão programados à força, mesmo se previamente agendados.

Nenhuma das opções de importação de URI faz com que os URIs sejam tratados como seeds ou alteram o escopo de qualquer maneira. (Por exemplo, você poderia forçar a programação de um URI fora do escopo - mas se ainda tiver o processador padrão 'Preselector' que verifica novamente o escopo, ele ainda será rejeitado do rastreamento quando for exibido.)

**Via JMX

A interface de controle remoto do JMX inclui as operações importUri e importUris no bean CrawlJob que imitam a função de importação de URIs da IUW. Um exemplo:

Exemplo de importação de JMX

### Parâmetros politeness

### Script BeanShell para download de vídeos

Aqui está um script BeanShell que pode ser usado para encontrar links de vídeo com o Heritrix. Atualmente, o script suporta apenas o youtube. Para fazer com que o script funcione, ele deve ser adicionado na fase de extração da cadeia do processador. Esse processo foi testado com o Heritrix 2.0.2 com uma sheet que limita o max-hops no youtube para 0, permitindo obter a primeira página dos vídeos do Youtube. Também é visível no modo proxy do Wayback 1.4.0. O script também funciona com vídeos do YouTube incorporados que usam "get_video_info" para recuperar tokens de vídeo.

Quaisquer comentários ou sugestões para melhorias do script são bem-vindos.

```
// This is a beanshell processor that will allow downloading of vidoes in Heritrix.
// Currently only supports Youtube video pages or embedded youtube video that use "get_video_info".
//
// The script should be added at the extraction part of the processor chain in the Heritrix profile.
//
// NOTE:  The video URIs that are extracted will automatically be added as
// candidate URIs (normally this is done by LinksScoper) to ensure that they are always downloaded.
// This can be changed if needed to instead have them added as outlinks so
// that LinkScoper does a scope check after extraction.
//
// Author:  Adam Taylor (adam dot taylor at lac-bac dot gc dot ca)

import java.io.IOException;
import java.net.URL;
import java.net.URLEncoder;
import java.net.URLDecoder;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import org.apache.commons.httpclient.URIException;
import org.archive.crawler.datamodel.CrawlURI;
import org.archive.crawler.datamodel.SchedulingConstants;
import org.archive.net.UURI;
import org.archive.net.UURIFactory;
import org.archive.io.ReplayCharSequence;
import org.archive.modules.extractor.Hop;
import org.archive.modules.extractor.Link;
import org.archive.modules.extractor.LinkContext;
import org.archive.modules.fetcher.FetchStatusCodes;

    process(CrawlURI curi) {
        if (curi.toString().matches(".*www\\.youtube\\.com/watch\\?.*")) {

            ReplayCharSequence cs = null;
            cs = curi.getRecorder().getReplayCharSequence();

            Matcher m = Pattern.compile(".*fullscreenUrl.*video_id=([^&]+).*t=([^&]+)").matcher(cs);
            m.find();
            String videoId = m.group(1);
            String token = m.group(2);
            String getVideoUrl = "http://www.youtube.com/get_video?video_id=" + videoId + "&t=" + token + "&el=detailpage&ps=";

            Link getVideoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoUrl), LinkContext.EMBED_MISC, Hop.EMBED);
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoLink1, SchedulingConstants.HIGH, false));
        }
        if (curi.toString().matches(".*youtube\\.com/swf/l\\.swf.*")) {
            Matcher swfParamMatcher = Pattern.compile(".*swf=([^&]+).*video_id=([^&]+).*eurl=([^&]*).*iurl=([^&]+).*").matcher(curi.toString());
            swfParamMatcher.find();

            String swfUrl = swfParamMatcher.group(1);
            String videoId = swfParamMatcher.group(2);
            String eurl = swfParamMatcher.group(3);
            String iurl = swfParamMatcher.group(4);

            String iurlDecoded = URLDecoder.decode(iurl);
            String swfUrlDecoded = URLDecoder.decode(swfUrl);
            String iurlCrossdomainUrl = "http://" + new URL(iurlDecoded).getHost() + "/crossdomain.xml";
            String youtubeCrossdomainUrl = "http://www.youtube.com/crossdomain.xml";

            String getVideoInfoUrl1 = "http://www.youtube.com/get_video_info?&video_id="
                + videoId
                + "&el=embedded&ps=default&eurl="
                + eurl;

            if (curi.toString().matches(".*hl=[^&]+.*")) {
                Matcher hlMatcher = Pattern.compile(".*hl=([^&]+).*").matcher(curi.toString());
                hlMatcher.find();
                getVideoInfoUrl1 += "&hl=" + hlMatcher.group(1);
            }

            Link getVideoInfoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoInfoUrl1), LinkContext.EMBED_MISC, Hop.EMBED);
            Link swfLink = new Link (curi.getUURI(), UURIFactory.getInstance(swfUrlDecoded), LinkContext.EMBED_MISC, Hop.EMBED);
            Link iurlLink = new Link (curi.getUURI(), UURIFactory.getInstance(iurlDecoded), LinkContext.EMBED_MISC, Hop.EMBED);
            Link iurlCrossdomainLink = new Link (curi.getUURI(), UURIFactory.getInstance(iurlCrossdomainUrl), LinkContext.EMBED_MISC, Hop.EMBED);
            Link youtubeCrossdomainLink = new Link (curi.getUURI(), UURIFactory.getInstance(youtubeCrossdomainUrl), LinkContext.EMBED_MISC, Hop.EMBED);

            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoInfoLink1, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), swfLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), iurlLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), iurlCrossdomainLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), youtubeCrossdomainLink, SchedulingConstants.HIGH, false));
        }

        if (curi.toString().matches(".*youtube\\.com/get_video_info\\?.*")) {
            Matcher videoInfoMatcher = Pattern.compile(".*video_id=([^&]+).*eurl=([^&]*).*").matcher(curi.toString());
            videoInfoMatcher.find();

            String videoId = videoInfoMatcher.group(1);
            String eurl = videoInfoMatcher.group(2);

            ReplayCharSequence cs = null;
            cs = curi.getRecorder().getReplayCharSequence();

            Matcher tokenMatcher = Pattern.compile(".*token=([^&]+).*").matcher(cs);
            tokenMatcher.find();
            String videoToken = tokenMatcher.group(1);

            String getVideoUrl1 = "http://www.youtube.com/get_video?video_id="
                + videoId
                + "&t="
                + videoToken
                + "&eurl="
                + eurl
                + "&el=embedded&ps=default";

            Link getVideoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoUrl1), LinkContext.EMBED_MISC, Hop.EMBED);

            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoLink1, SchedulingConstants.HIGH, false));
        }
    }
```

### craw manifest

Exemplo de um arquivo *crawl manifest* do Heritrix 1.15

crawl-manifest.txt

L+ /1/crawldata/CRAWL-03-16-2010/logs/crawl.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/runtime-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/local-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/uri-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/progress-statistics.log
C+ /0/jobs/CRAWL-03-16-2010/order.xml
C+ /0/jobs/CRAWL-03-16-2010/settings/com/youtube/settings.xml
C+ /0/jobs/CRAWL-03-16-2010/seeds.txt
R+ /1/crawldata/CRAWL-03-16-2010/hosts-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/mimetype-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/responsecode-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/seeds-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/crawl-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/processors-report.txt

L+: arquivos log
C+: arquivos config
R+: arquivos de relatório

O *crawl manifest* é gerado ao clicar em "terminate" para uma tarefa na interface da Web do Heritrix. Pode levar algum tempo para criar o arquivo (20 minutos?), então seja paciente e não desligue o Heritrix muito cedo.

### Opções JVM

Notas diversas sobre as opções JVM de interesse para os operadores do Heritrix. 

Recomendado:

-XX: + UseParallelOldGC

Pode ser útil para acelerar compactações completas de GC em máquinas com vários núcleos. Ver http://java.sun.com/javase/technologies/hotspot/gc/gc_tuning_6.html#par_gc

-XX: + DoEscapeAnalysis

Disponível em 6u14, pode melhorar o desempenho; ver http://java.sun.com/javase/6/webnotes/6u14.html

-XX: + UseCompressedOops

Disponível em 6u14, pode melhorar o uso de memória/GC if using 64-bit JVM on heaps <32GB in size;; ver http://java.sun.com/javase/6/webnotes/6u14.html

Talvez:

-XX: -UseGCOverheadLimit

Desativa um OOME que não é criado quando a memória está realmente esgotada, mas quando uma quantidade excessiva de tempo é gasta no GC. We've seen this OOME at times when a requested memory-intensive report may have completed if not for this OOME (albeit in an extreme length of time).

### Frontier queue budgets

### 

Configuração para capturar o material que é carregado quando você scroll down após o final da página no Facebook e no Twitter usando o novo ExtractorMultipleRegex:

**Twitter**

Configurar *extractors* no nível superior:

```
<bean id="extractorTwitterScrollOne" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?twitter\.com/([^/]+)/?(?:\?.*)?$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="maxId" value="data-max-id=&quot;(\d+)&quot;"/>
                        </map>
                </property>
                <property name="template">
                        <value>/i/profiles/show/${uriRegex[1]}/timeline/with_replies?include_available_features=1&amp;include_entities=1&amp;max_id=${maxId[1]}</value>
                </property>
        </bean>
        <bean id="extractorTwitterScrollFurther" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?twitter\.com/i/profiles/show/([^/]+)/timeline/with_replies\?include_available_features=1&amp;include_entities=1&amp;max_id=\d+$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="maxId" value="&quot;max_id&quot;:&quot;(\d+)&quot;"/>
                        </map>
                </property>
                <property name="template">
                        <value>/i/profiles/show/${uriRegex[1]}/timeline/with_replies?include_available_features=1&amp;include_entities=1&amp;max_id=${maxId[1]}</value>
                </property>
        </bean>
```

Inserir na fetch chain:

```
 <ref bean="extractorHtml"/>
                                <ref bean="extractorCss"/>
                                <ref bean="extractorJs"/>
+                                <ref bean="extractorTwitterScrollOne"/>
+                                <ref bean="extractorTwitterScrollFurther"/> 
                        </list>
                </property
```

Use sheets para permitir URLs relevantes:

```
<bean id="twitterScrollOne" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorTwitterScrollOne.enabled" value="true"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,twitter,)/</value>
                                <value>http://(com,twitter,www,)/</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>twitterScrollOne</value>
                        </list>
                </property>
        </bean>
        <bean id="twitterScrollFurther" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorTwitterScrollFurther.enabled" value="true"/>
                                <entry key="extractorTwitterScrollOne.enabled" value="false"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,twitter,)/i/profiles/show/</value>
                                <value>http://(com,twitter,www,)/i/profiles/show/</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>twitterScrollFurther</value>
                        </list>
                </property>
        </bean>
```

**Facebook**

Configurar *extractor* no nível superior:

```
<bean id="extractorFacebookScroll" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?facebook\.com/[^/?]+$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="jsonBlob" value="\[&quot;TimelineContentLoader&quot;,&quot;registerTimePeriod&quot;,[^,]+,[^,]+,[^,]+,\{(&quot;profile_id&quot;:[^}]+)\},false,null,(\d+),"/>
                                <entry key="ajaxpipeToken" value="&quot;ajaxpipe_token&quot;:&quot;([^&quot;]+)&quot;"/>
                                <entry key="timeCutoff" value="&quot;setTimeCutoff&quot;,[^,]*,\[(\d+)\]\]"/>
                        </map>
                </property>
                <property name="template">
                        <value>/ajax/pagelet/generic.php/ProfileTimelineSectionPagelet?ajaxpipe=1&amp;ajaxpipe_token=${ajaxpipeToken[1]}&amp;no_script_path=1&amp;data=${java.net.URLEncoder.encode('{' + jsonBlob[1] , 'UTF-8')},&quot;time_cutoff&quot;%3A${java.net.URLEncoder.encode(timeCutoff[1] , 'UTF-8')},&quot;force_no_friend_activity&quot;%3Afalse%7D&amp;__user=0&amp;__a=1&amp;__adt=${jsonBlob[2]}</value>
                </property>
        </bean>
```

Inserir na fetch chain:

```
 <ref bean="extractorCss"/>
                                <ref bean="extractorJs"/>
                                <ref bean="extractorTwitterScrollOne"/>
                                <ref bean="extractorTwitterScrollFurther"/>
+                                <ref bean="extractorFacebookScroll"/>
                        </list>
                </property>
```

Use sheets para permitir URLs relevantes:

```
<bean id="enableFacebookScroll" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorFacebookScroll.enabled" value="true"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,facebook,</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>enableFacebookScroll</value>
                        </list>
                </property>
        </bean>
```

### Deduping (redução de duplicação)

A partir do release 1.12.0, vários Processadores podem cooperar para transportar o histórico de conteúdo do URI entre os rastreamentos (consulte JavaDocs do pacote org.archive.crawler.processor.recrawl). Isso reduz a quantidade de material duplicado baixado ou armazenado em rastreamentos posteriores.

### Configuração de desduplicação H1

O Heritrix 1.x não suporta a execução de um mesmo rastreamento mais de uma vez, portanto, um rastreamento precisará ser configurado para *armazenar* dados de redução de duplicação, e o outro rastreamento precisará ser configurado para fazer o *carregamento* de dados de redução de duplicação. Exemplo (trecho do teste para o HER-1627):

**configure persist STORE crawl**

* adicione os processadores FetchHistory e PersistLog depois de `FetchHttp`
`org.archive.crawler.processor.recrawl.FetchHistoryProcessor`
`org.archive.crawler.processor.recrawl.PersistLogProcessor`

**configure persist LOAD crawl**

* *depois* de PreconditionEnforcer, *antes* de FetchDNS
`org.archive.crawler.processor.recrawl.PersistLoadProcessor`
* *depois* de FetchHTTP
`org.archive.crawler.processor.recrawl.FetchHistoryProcessor`
* preload-source:
`${HERITRIX_HOME}/jobs/${JOB}/logs/persistlog.txtser.gz`

### Configuração de desduplicação H3

O Heritrix 3.x permite executar o mesmo rastreamento repetidamente, mas exige uma configuração diferente para a execução de rastreamentos que *armazenam* dados de deduplicação e a execução de rastreamento que *carregam* dados de redução de redundância conforme descrito em Processadores de Redução de Duplicação. O mesmo modelo é seguido para o H1, exceto pelo uso do beans CXML do rastreador do Spring-world (`crawler-beans.cxml`) para configuração.

### Force speculative embed URIs into single queue

O uso da configuração a seguir enviará incorporações especulativas (hoppath 'X' encontrado por meio da extração do javascript) em uma única fila separada.

Isso é necessário devido ao extrator de javascript que enfileirava um grande número de URIs defeituosos/inexistentes.

```
<!-- extracted uris go to their own queue -->
<bean class='org.archive.crawler.spring.DecideRuledSheetAssociation'>
  <property name='rules'>
    <bean>
     <property name="regex" value=".*X$" />
    </bean>
  </property>
  <property name='targetSheetNames'>
   <list>
    <value>speculativeSingleQueue</value>
    <value>speculativeSingleQueueBalance</value>
   </list>
  </property>
</bean> 

<bean id='speculativeSingleQueue' class='org.archive.spring.Sheet'>
  <property name='map'>
   <map>
    <entry key='queueAssignmentPolicy.forceQueueAssignment' value='speculative-queue' />
   </map>
  </property>
 </bean>
 <bean id='speculativeSingleQueueBalance' class='org.archive.spring.Sheet'>
   <property name='map'>
     <map>
       <entry key='frontier.balanceReplenishAmount' value='2000000000' />
     </map>
   </property>
</bean>
```

### Scripts úteis do Heritrix3

Scripts úteis do Heritrix 3 H3 para executar no console de script.






