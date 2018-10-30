# Guia do usuario Heritrix
Introdução:
Heritrix é o rastreador do Internet Archive com qualidade de arquivamento extensível, escálavel e de código aberto.
Esse guia explica como instalar , configurar e usar o Heritrix para rastrear a web. Assume-se que o usuário possui um conhecimento geral de conceitos computacionais como HTML e URLs.

Usuários:
Os usuários desse documento constituem-se de administradores do Heritrix e outras equipes técnicas que desejam crawl a internet usando o Heritrix.

Versões:
As informações desse guia referem-se a versão 3.0 do Heritrix, a não ser que o contŕário seja informado. Seções referentes à versão 3.1 estão marcadas com a nota "Na versão 3.1".

Requerimentos do sistema

Atualizações das versões 3.0 e 3.1
Atualizações da versão 3.0:
Possibilidade de realizar vários rastreamentos simultaneos. O único limite de número de rastreamentos realizados simultaneamente refere-se a memória alocada no Heritrix.
Arquivo único XML de configuração baseado no framework Spring. Esse arquivo substitui o order.xml e outros arquivos de configuração do Heritrix 1.x.
Possibilidade de busca e modificação dos beans configurados por um navegador de uso simples. Olhar Bean Browser.
Extensabilidade aprimorada por meio do framework Spring. Por exemplo, substituições de domínios podem ser definidas em um nível mais refinado. Olhar Sheets.
Controle de uso mais seguro. HTTPS é usada para acessar e manipular o controle de uso do usuário.
Maior escabilidade.  Nas versões anteriores, rastreamentos with large seed values (tens or hundreds of millions) podiam tentar utilizar memória além da alocada no Heritrix. Isso causava o interrompimento do rastreamento. A versão Heritrix 3.0 arrumou esses problemas, permitindo o processamento estável de rastreamentos de grande tamanho.
Flexibilidade maior ao modificar um rastreamento em andamento. Rastreamentos em andamento podem ser modificados pelo navegador Bean ou pelo Action Directory.
Introdução de filas paralelas. Ao rastrear sites específicos que aguentam tráficos altos, a opção das filas paralelas pode ser usada para abrir várias conexões de rastreamento concomitantes em um único site.
Controle de script que aceita input de scripts em vários formatos, como o AppleScript e o ECMAScript. Scripting pode ser usado para acessar e manipular, de forma programada, os componentes do núcleo do Heritrix.

Instação do Heritrix
Uma distribuição binária do Heritrix pode ser baixada pelo link http://builds.archive.org:8080/maven2/org/archive/heritrix/heritrix/3.1.1/heritrix-3.1.1-dist.zip. Depois de baixado, expandir o arquivo. Esse processo funciona em algumas plataformas.

Configuração do Heritrix

Execução do Heritrix 3.0 e 3.1
O Heritrix pode ser usado a partir de diferentes linhas de comando. Insira o seguinte comando para visualizar as opções disponíveis. 
$HERITRIX_HOME/bin/heritrix --help
A tabela a seguir lista as opções de linhas de comando.

Interface do usuário baseada na Web (IUW)
Depois de abrir o Heritrix, a interface do usuário baseada na web torna-se acessível.

A URI de acesso da Web IU normalmente é
https://(heritrixhost):8443

A IUW é protegida por senha. Não há nenhum login de acesso padrão. Um nome de usuário e uma senha devem ser fornecidos usando as opções de linha de comando -a ou -web-admin fornecidas no startup ou definindo a propriedade do sistema heritrix.cmdline.admin. O nome de usuário e a senha ficarão salvas (printed) no controle no startup. Na versão 3.1, o nome de usuário do administrador e a senha não ficarão salvas no controle no startup. Também, na versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador, adicionando mais uma medida de proteção ao nome de usuário e senha do administrador.

A página inicial de login solicita o nome de usuário e a senha. Depois de feito o login, a sessão será encerrada após um período sem uso.

O acesso da IUW é por HTTPS. O Heritrix é instalado com uma chave de acesso que contém um certificado auto-assinado. Isso fará com que o Mozila (navegador recomendado) exiba um prompt avisando que um certificaco auto-assinado está sendo usado. Siga as instruções abaixo para realizar o login no Heritrix pela primeira vez.
