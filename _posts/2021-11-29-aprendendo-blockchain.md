---
layout: post
title:  "Aprendendo em público: blockchain e criptomoedas"
date:   2021-11-29 00:06:03 -0300
---
Tenho ouvido em todas minhas bolhas sociais sobre criptomoedas, bitcoin e NFT, mas não entendo nada do assunto. E penso que eu que trabalho com programação deveria ter um bom entendimento. Então já é hora de acabar com isso! Aqui neste primeiro post eu vou registrar minhas perguntas e respostas que for encontrando, tentando documentar a minha linha de raciocínio durante meu aprendizado.

As perguntas iniciais que tenho em mente são:
* O que é e como funciona a blockchain?
* O que é criptomoeda e bitcoin?
* Por que criptomoeda tem valor?
* Por que o valor das criptomoedas variam tanto?
* Por que hoje as criptomoedas estão valendo muito?
* O que é NFT?
* Criptomoeda é realmente a moeda do futuro ou existe outra solução?

Então vamos começar entendendo o que é blockchain.

Bom, depois de ler alguns artigos[1][2] e assistir alguns vídeos[3], o que tenho a dizer com minhas palavras é que blockchain é uma base de dados que registra o histórico de transações e que armazena esses dados de forma descentralizada, ou seja, esses históricos não ficam armazenados em apenas um computador, mas sim em todos os computadores que utilizam esse banco de dados.

Aqui vai minha primeira epifania: o blockchain é uma tecnologia inicialmente independente do bitcoin ou qualquer criptomoeda.

Vou dar um exemplo prático: Joana tem um carro e ela vai vendê-lo para Maria. Para isso, após o negócio fechado entre as duas, Joana deve fazer todo o processo de transferência de titularidade do veículo no Departamento Estadual de Trânsito (DETRANs), que detem um banco de dados nacional onde tem o registro de que Joana é a atual proprietária do carro. Ao fazer a transferência, vai ficar registrado no histórico do veículo que “Joana vendeu o carro para Maria”.

Perceba uma coisa: o banco de dados é de propriedade do DETRAN e só ele pode verificar que seus dados não foram corrompidos. E se Paulo, um hacker muito talentoso, quisesse passar o carro de Joana para seu próprio nome? Ele simplesmente invadiria o sistema do DETRAN e trocaria o registro para seu nome e pronto, ninguém contestaria os dados.

O blockchain resolve esses dois principais problemas: a centralização e a corrupção dos dados.

Vamos entender como o blockchain descentraliza o armazenamento dos dados.

Funcionaria assim: cada pessoa teria uma cópia completa do banco de dados em seu computador e cada mudança que acontecesse seria refletida no computador de todo mundo através da internet. Então no exemplo acima, a Joana, ao invés de ir no DETRAN, simplesmente abriria o seu computador, entraria com sua conta e senha no sistema e alteraria a titularidade do seu carro para Maria e essa transação surtiria efeito no computador de todo mundo.

Por que a descentralização é uma coisa boa?
Geralmente, a descentralização sempre é bom pois assim o controle não fica na mão de apenas uma instituição ou pessoa. No caso de um banco de dados, também há o fato de que se este fosse armazenado num único servidor do DETRAN ele estaria sujeito a perda de todos os seus registros caso acontecesse algum acidente corrompendo seus dados. Mas tendo uma cópia completa em cada computador, a corrupção dos dados teria que ocorrer em todos os computadores.

E sobre a corrupção dos dados?
Blockchain significa corrente de blocos e, assim como em uma corrente, a quebra de um único nó compromete a corrente inteira. 

O que são esses blocos de dados?
Cada bloco de dados da blockchain é composto por três fatores:
Os seus dados: as transações, como por exemplo “Joana vendeu o carro para Maria”.
A sua impressão digital, que em computação é chamada de hash.
E a impressão digital (hash) do bloco anterior.

Sendo assim, a corrente é formada pela ligação que cada bloco tem com o bloco anterior.
Aí você me pergunta: tá, mas o que é esse tal de hash? Pois bem, o hash é uma cadeia de 64 letras e números que é formada a partir de um dado. No nosso caso, estamos usando o dado “Joana vendeu o carro para Maria”. O hash para o nosso dado é o seguinte (gerado aqui):

1d4140f617daa6fca841970bc05654f99a8e4ff37cda82acfb6331c5d54ae383

Bem doido, né? O que faz o hash ser tão especial é que existe um único para cada dado, seja ele qual for. Isso é calculado através de um algoritmo chamado SHA256, que eu ainda não sei como funciona, mas qualquer dia vou querer dar uma olhada mais a fundo.

Então voltando à cadeia: se um bloco tem o seu hash e o do bloco anterior, caso haja alguma alteração no bloco anterior, todos os blocos posteriores vão acusar que tem coisa errada ali. Por isso a segurança contra corrupção.

Se você for neste [link](https://andersbrownworth.com/blockchain/hash), pode ver qual o hash gerado para o dado que escrever.

Vou experimentar mais um pouco: meu primeiro bloco de dados vai ter as seguintes informações:
O seu dado: “Joana comprou um carro”;
O hash do bloco anterior vai ser “00000” pois não existe bloco anterior.
Seu próprio hash: 6dece74df8d16ac04568a4f6374daa7e0647a25662813d99bd7eb47c7689a1b7

![Print do bloco um](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/410py7l0h3a0a7spfge0.png)

Agora meu segundo bloco vai ter as seguintes informações:
Joana vendeu seu carro para Maria
Hash anterior: 6dece74df8d16ac04568a4f6374daa7e0647a25662813d99bd7eb47c7689a1b7
Seu Hash: 0a3353754007af8414bfb31ad4aca356120151453686e517fdd2e82d09c3d278

![Print do bloco 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u53lljfqpze3f7uj8doe.png)

Se o primeiro bloco tiver qualquer alteração, o seu hash vai mudar. E como o segundo bloco tem registrado o hash original do primeiro bloco, ele vai comparar com o hash alterado e vai acusar que tem informação alterada ali!
Então dessa forma a cadeia de blocos garante que seus dados não foram corrompidos.

Navegando por este site de demonstração de blockchain[4], você pode encontrar as páginas que demonstram melhor e também os vídeos de explicação, que eu achei ótimos vale dar uma olhada.

Ao assistir os vídeos que falei ali em cima, me deparei com alguns outros conceitos:
* A assinatura de bloco
* O número Nonce
* E a mineração de um bloco

Esses três conceitos são interligados, então serão explicados juntos.

Sabemos que uma rede blockchain não depende de um único servidor, sendo assim, qualquer um com acesso a rede pode gravar dados na blockchain. É claro que cada um deve ter sua conta e senha para fazer qualquer operação, mas mesmo assim, usuários maliciosos podem fazer uma coisa chamada ataque de negação de serviço (também conhecido como DoS Attack, um acrônimo em inglês para Denial of Service). Esse tipo de ataque consiste em enviar vários dados para a rede fazendo com que esta se sobrecarregue e então “trave” ou pare de responder. E para prevenir este ataque a blockchain requer que quem quer enviar dados à rede tenha um trabalho computacional. Aqui nos deparamos com o conceito de “Prova de trabalho”.

Essa prova de trabalho consiste em encontrar um número que, ao ser adicionado ao corpo do bloco a ser criado, gere um hash específico.

Este “número mágico” é chamado de nonce[5], acrônimo para number once, ou “número único”, pois ele realmente é o único número que pode satisfazer uma condição.

E o ato de procurar esse número é chamado de mineração.

Vamos voltar ao exemplo de Joana vendendo seu carro.

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jtu6m7zzibiiuxasjd2e.png)

Na imagem acima, que é do site já citado para demonstrações de blockchain, Temos um bloco que tem número 1, tem o nonce 72608, o dado “Joana vendeu seu carro para Maria” e o seu hash. O bloco está vermelho pois o seu hash não satisfaz a condição, que no caso é que “o hash deve começar com quatro dígitos zeros”. Essa condição varia com o tempo para dificultar ou facilitar a prova de trabalho.
Então, para chegar ao hash que satisfaça a condição, o computador vai testando vários números que, junto com o dado e o número do bloco, vão gerar um hash que inicie com quatro zeros.

Ao clicar no botão “mine”, vamos minerar e chegar ao resultado correto.

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rrpdcxtybbst7y7ndvfc.png)

Agora nosso bloco está verde, indicando que foi validado, ou assinado, e o seu nonce é 62449, fazendo com que o seu hash inicie com 4 dígitos zeros.

Se você for no site e testar, verá que ao clicar em “Mine” demora pelo menos um segundo para chegar ao resultado correto, ou seja, não é um número fácil de ser calculado e isso impede que alguém com más intenções tente publicar milhares de blocos de uma só vez na rede.

E aqui me veio um esclarecimento bem grande: a mineração é necessária apenas por causa da camada de segurança. O que eu quero dizer é que, a “lógica base” da blockchain, que é registra dados em cadeia, não precisa encontrar um número que satisfaça uma condição. Isso era uma coisa que me deixava beem confuso esse conceito de minerar bitcoin. Olha só, na real o que é minerado é o nonce!

Bom, acredito que aqui já cheguei num novo nível de entendimento sobre a blockchain e com novas perguntas que tentarei entender no próximo post:
Se o que é minerado é o nonce, por que existe a confusão de “minerar bitcoin”?
O que acontece se dois computadores descobrirem o noce ao mesmo tempo?

Fico por aqui e até o próximo post!

[1] https://pt.wikipedia.org/wiki/Blockchain

[2] https://blog.nubank.com.br/o-que-e-blockchain/

[3] https://www.youtube.com/watch?v=dkElPTevoR4

[4] https://andersbrownworth.com/blockchain/

[5] https://youtu.be/rZaGbEcs7ag


