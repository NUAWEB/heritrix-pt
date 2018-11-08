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

A IUW é protegida por senha. Não há nenhum login de acesso padrão. Um nome de usuário e uma senha devem ser fornecidos usando as opções de linha de comando -a ou -web-admin fornecidas no startup ou definindo a propriedade do sistema heritrix.cmdline.admin. O nome de usuário e a senha ficarão salvas (printed) no controle no startup. A partir da versão 3.1, o nome de usuário do administrador e a senha não ficarão salvas no controle no startup. Também, a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador, adicionando mais uma medida de proteção ao nome de usuário e senha do administrador.

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

1. Insira o nome do novo trabalho na caixa de texto abaixo de "Criar novo trabalho com configuração inicial recomendada". Depois clique em "criar".

O trabalho recémn criado aparecerá na lista de trabalhos na página do Controle Principal. No Heritrix 3.0, o trabalho será baseado no perfil profile-defaults. A partir da versão 3.1, esse perfil foi eliminado. Ver Perfis para mais informações.

2. Clique no nome do novo trabalho e você será redirecionado para a página do trabalho.
O nome do arquivo de configuração, crawler-beans.cxml, aparecerá no topo da página. Ao lado, encontra-se a opção de "editar".

3. CLique em "editar" e os conteúdos do arquivo de configuração aparecerão em uma área de texto editável.

4. Nesse passo, várias propriedades devem ser inseridas para o trabalho tornar-se executável.
i. Primeiro, adicione um value válido na propriedade metadata.operatorContactUrl, como http://www.archive.org. 
ii. Em seguida, preencha o elemento `<prop>` do bean `longOverrides` com os valores do seed para o rastreamento.  Um seed de teste é configurado para referência. Quando terminar, clique em "salvar mudanças" no topo da página. Para mais informações sobre configuração de trabalhos ver Configurando Trabalhos e Perfis. 
  
5. Na página do trabalho, clique em "montar". Esse comando montará a infraestrutura necessária para executar o trabalho. A seguinte mensagem será exibida no registro de tarefas: "INFO JOB instanciado".

6. Em seguida, clique no botão "iniciar". Este comando inicia o trabalho no modo "pausado". Nesse passo, o trabalho está pronto para ser executado.

7. Para executar o trabalho, clique no botão de despausar. O trabalho, então, começará a enviar solicitações para as sementes do seu rastreamento. O status do trabalho será definido como "Em execução". Atualize a página para acompanhar as atualizações das estatísticas.

### Observação

* Um trabalho não será modificado se o perfil ou o trabalho em que foi baseado for alterado.
* Trabalhos baseados no perfil padrão não estão prontos para serem executados. O metadata.operatorContactUrl deve ser definido com um valor válido.

Mais informações sobre a avaliação do progresso de um trabalho podem ser encontradas em Análise de Tarefas.

### Anexos

## Sair do Heritrix

Para sair do Heritrix, envie a combinação de teclas para o controle, o que forçará o processo a sair (como o Control-C). Para sair de um processo em segundo plano no qual o Heritrix está em execução, use o comando kill.

Para sair da versão 3.1, clique no botão "Sair do processo Java" depois de marcar "Tenho certeza".

## Editar um trabalho em andamento

A configuração de um trabalho pode ser editada enquanto ele ainda está em andamento. Isso pode ser feito pelo Bean Browser ou pelo link do Controle de Script na página do trabalho. O Bean Browser permite que as propriedades de tempo de execução sejam editadas.

O controle de script também pode ser usado para editar programaticamente trabalhos em execução.

Se um valor não atômico for alterado, é recomendável pausar o rastreamento antes de fazer a alteração, pois algumas modificações nas entidades de configuração composta podem não ocorrer de maneira segura. Um exemplo de uma mudança não atômica é a adição de uma nova planilha.

A partir da versão 3.1, o tratamento de operações de orçamento de fila e de rotação/retirada foi refatorado para garantir que alterações feitas  durante o rastreamento (por meio de novas Sheets sobrepostas ou edição direta com a ferramenta Bean Browser ou controle de script) entrem em vigor imediatamente. O DispositionProcessor possui configuração de sobreposição de Sheets para aplicar essa marcação. Em geral, as alterações de configurações por meio de novas planilhas e associações de planilhas durante um rastreamento (conforme inserido via script) agora entram em vigor em todos os URIs que estão sendo retirados para processamento, em vez de apenas URIs recém-descobertos. Portanto, alterações feitas via bean-browse/scripting /new-sheet-overlays entram em vigor imediatamente.

## FTP

Heritrix suporta rastreamento de sites FTP. Seeds devem ser adicionados no seguinte formato: ftp://ftphostname.org/ftpdirectory.

## Ponto de verificação (Checkpoint)

O ponto de verificação de um trabalho de rastreamento salva uma representação do estado atual do trabalho em um diretório no `checkpointsPath`, que recebe o nome do ponto de verificação. Essa representação inclui a serialização dos principais objetos do trabalho de rastreamento, cópias do conjunto atual de arquivos de log de bdbje e outros arquivos do estado do rastreamento.  O diretório do ponto de verificação contém tudo o que é necessário para recuperar um rastreamento. O ponto de verificação também roda os logs de rastreamento, incluindo o log `recover.gz`, se ativado. Arquivos Log não são copiados para o diretório do ponto de verificação e permanecem no diretógio de `logs`, sendo diferenciados através de sufixos. O sufixo é o nome do ponto de verificação; por exemplo, para o ponto `000031` o log do rastreamento será `crawl.log.000031`.

Para executar um ponto de verificação, clique no botão do ponto de verificação na página de trabalho da IUW ou invoque a funcionalidade do ponto de verificação pelo REST API. O ponto de verificação pode levar mais tempo em rastreamentos maiores (a partir da versão 3.1 esse processo é muito mais rápido). Enquanto o ponto de verificação estiver sendo feito, o status do rastreamento ficará como CHECKPOINTING. Após o término, o rastreador continuará o rastreamento, a não ser que ele estivesse pausado anteriormente. Nesse caso, o rastreamento continuará pausado.

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
3. Desmonte o trabalho clicando no botão "desmontar". 
4. Reconstrua o trabalho clicando no botão "construir". Agora, uma caixa suspensa deve aparecer em cima dos botões de comando. A caixa suspensa tem os nomes dos pontos de verificação invocados anteriormente.
5. Selecione um checkpoint da caixa. O checkpoint selecionado será usado para iniciar o trabalho recém-construído.
6. Clique em "iniciar".
7. Clique em "despausar".
O trabalho iniciará a partir do ponto de verificação escolhido.

## Página do Controle Principal

A página do controle principal aparece logo após o login. Esse página lista todos os trabalhos e perfis.

### Elementos e operações de dados do controle principal

reexaminar

O botão de "reexaminar" faz com que o Heritrix examine o sistema de arquivos procurando qualquer mudança no diretório "trabalhos". A exibição é, então, sincronizada com o sistema de arquivos

criar

O botão "criar" permite que um nome seja inserido e um novo trabalho de rastreamento seja criado. O trabalho de rastreamento será baseado nos perfis padrões.

adicionar

O botão "adicinar" permite que um diretório de trabalho não gerenciado pelo Heritrix seja especificado. Após inserir o caminho para o novo diretório e clicar "adicionar", o Heritrix permitirá que você administre o diretório. Por exemplo, será possível configurar o trabalho usando o arquivo crawler-beans.cxml.

status

O status dos trabalhos em andamento, o número de vezes que um trabalho foi iniciado e o caminho para o arquivo de configuração dos trabalhos aparecem na página do Controle Principal. Também aparecem as estatísticas de memória do Heritrix e se o trabalho é um perfil ou não.

Encerrar o Processo Java

A partir da versão 3.1, existe o botão "Encerrar o Processo Java". Após ser selecionado juntamente com a seleção da opção "Tenho certeza", esse botão fará com que o Heritrix seja encerrado e fechado.

## Beans Browser

A IUW fornece uma maneira de visualizar e editar os beans Spring que formam a configuração de um rastreamento. É importante notar que mudar a configuração de um rastreamento usando o Bean Browser não fará com que o arquivo crawler-beans.cxml seja atualizado. Portanto, mudanças de definições feitas com o Beans Browser não são permanente. O Bean Browser deve ser usado apenas para mudar as definições de um rastreamento em andamento.  Para acessar o navegador, clique no link do Bean Browser na página de trabalhos. A hierarquia dos beans Spring aparecerá.

Os beans individuais podem ser detalhados ao clilcar neles. O exemplo abaixo mostra a exibição depois de clicar nos seeds beans. 

## Perfis

Perfis são modelos para a criação de trabalhos de rastreamento. Contêm todas as configurações de um trabalho de rastreamento, mas não são considerados rastreáveis. O Heritrix não permite que um perfil seja diretamente rastreado, apenas trabalhos baseados em perfis.

Um exemplo comum de uma configuração de perfil é deixar a propriedade `metadata.operatorContactUrl` indefinida, forçando o operador a inserir um valor válido.

Os perfis podem ser usados como modelos ao deixar suas definições de configuração inválidas. Dessa meneira, o operador é obrigado a escolher suas configurações ao criar um trabalho a partir de um perfil. Isso pode ser vantajoso quando um administrador precisar configurar vários trabalhos de rastreamento diferentes para acomodar sua política de rastreamento.

Se um trabalho de rastreamento é um perfil ou um trabalho que pode ser iniciado é determinado por um nome de arquivo do arquivo de configuração principal. Se começar com "profile-", é um perfil. Tenha cuidado ao alterar o nome de um arquivo de configuração principal ao copiar manualmente o perfil para criar um trabalho de rastreamento possível de incialização.

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
Esse arquivo contém os formulários ﻿SURTs das URIs seed.

negative-surts.dump
Esse arquivo contém o formulário SUR de URIs que devem ser excluídos do rastreamento.

heritrix_out.log
This file captures output to standard out and standard error.  Most of the output consists of low-level exceptions and logging information.

Esse arquivo é criado no mesmo diretório do arquivo jar do Heritrix. Não é associado com nenhum trabalho específico, mas contém outputs de todos os trabalhos executados pelo rastreador.

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
