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

* Cadeia de candidatos (Chain Candidates):

- Processa os URIs de rastreamento de entrada, decidindo se deve mantê-los (de acordo com o escopo) e se eles serão depositados no Frontier.

- Ver Candidate Chain Processors

* Frontier:

- Os URIs de rastreamento aceitos nesse rastreamento são armazenados aqui em ordem de prioridade, em um conjunto de filas distintas.

- Normalmente, há uma fila por "autoridade" (por exemplo, `exemplo.com:80`) e o gerenciamento de filas garante que o atraso de rastreamento desejado seja respeitado para cada fila.

- Ver Frontier

* Cadeia de busca (Fetch Chain):

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

Se um arquivo `.recover.gz` estiver sendo usado, apenas um arquivo completo deve ser usado. (Isso é para que o diretório de ação processando um arquivo de cada vez possa fazer a primeira passagem completa de 'includes', em seguida, a passagem completa de 'horários', de um arquivo. O fornecimento de vários arquivos `.recover.gz` em série resultará em um ciclo includes/schedule, includes/schedule, etc., que não produzirá o efeito desejado no frontier.)


 




