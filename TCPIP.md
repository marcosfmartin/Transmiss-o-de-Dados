# Aula 2: Modelo TCP/IP

### Introdução

Primeiro é importante notar que os computadores não precisam estar ligados na mesma rede. Ou seja, o host A pode estar conectado em uma rede 
Wi-fi (através de um protocolo que utilize essa lógica na sua camada de acesso) enquanto o host B está conectado a uma rede que utiliza a lógica
ethernet (cabo de rede). Essas redes são chamadas de **subnetworks**. Esses protocolos na camada de acesso dão a capacidade dos hosts enviarem
dados através da subnetwork para um host na mesma rede ou, no caso do receptor estar conectado em outra subnetwork, para um roteador que irá 
encaminhar esses dados.



O protocolo IP é implementado em todos os end-systems (hosts) e em todos os roteadores. O protocolo TCP, por sua vez, só precisa ser
implementado nos hosts. Isso acontece porque o protocolo IP, na camada de rede, é o responsável por transmitir os dados de um computador 
pro outro, enquanto o protocolo TCP, na camada de transporte, só é responsável por levar os dados da rede para a aplicação e vice-versa. Como os roteadores
não têm aplicações que vão se utilizar dos dados, o protocolo TCP é desnecessário para eles.


**tl;dr**: O que dá a capacidade dos dados poderem atravessar diferentes tipo de conexões (ou seja, de um computador conectado no wi-fi conseguir receber dados de um computador conectado no cabo de rede) é o roteador. IP tem que ser implementado em todos os computadores que os dados passam. TCP apenas no emissor e receptor.


### Operações no modelo TCP/IP
Suponha que um processo que está ligado à porta 3 do host A queira enviar dados para um processo associado à porta 2 do host B.

Primeiro, a aplicação em A envia os dados para o TCP, mandando ele enviar para host B, porta 2. Isso é o salto da camada de aplicação para
a camada de transporte.

Segundo, o protocolo TCP vai enviar os dados para o protocolo IP, pedindo para que ele envie para o host B. Note que ele
não especifica a porta, já que o trabalho do IP é apenas levar até o host B. Salto da camada de transporte para a de internet.

Terceiro, o protocolo IP envia os dados para o protocolo que rege a camada de acesso à rede (ex: wi-fi, ethernet etc), com instruções para os dados serem
levados para um roteador J, a primeira parada no caminho de A até B.

Para emitir esses comandos, cada um dos protocolos, informações de controle tem que ser transmitidas juntos dos dados que estão sendo
enviados. No nosso exemplo, quando o TCP recebe os dados originais da aplicação em A, eles são quebrados em várias partes para 
serem mais manejáveis. Depois, a cada um desses blocos o TCP adiciona informações de controle, chamadas de header. O conjunto de dados + 
header é chamado de **TCP segment**. Alguns exemplos de informações no header são: porta de destino, origem, um numero para facilitar a
ordenação dos blocos de dados, checksum para verificar a existência de erros na transmissão etc. 

Depois, quando os TCP segments chegarem na camada de internet, o protocolo IP irá adicionar o seu próprio header, 
criando o chamado **IP datagram** (não me pergunte porque são nomes diferentes).
Um exemplo de informação nesse header é o endereço do host final (B). 

Por fim, o protocolo da camada de acesso à rede irá adicionar o seu próprio header, criando finalmente o **packet**.

Depois de todos os headers terem sido adicionados aos dados originais, os dados estão finalmente prontos para viajar. Assim, no nosso exemplo
a primeira parada é o roteador J. Nele, o header da camada de acesso à rede vai ser descartado (pois já cumpriu seu papel de levar até o roteador J)
e o IP datagram vai ser analisado para saber qual é o próximo passo. Ao analisar o IP datagram, o roteador saberá que tem que mandar o datagram
para o host B. Para fazer isso, o datagram é de novo aumentado com um header de acesso à rede. 

No host B, acontece finalmente o processo reverso. Cada header é retirado, e os bits remanescentes são levados para a camada seguinte, até que
apenas o dado original seja mandado para a aplicação original.

**tl;dr** Os dados são particionados e cada um dos protocolos adiciona infos de controles, chamadas de headers. Os dados viajam para o 
roteador, que examina para onde tem que mandar os dados e troca apenas o header da camada de acesso à rede. Depois, o dado viaja até o 
destino e lá é retirado todos os headers e apenas os dados originais são enviados para a aplicação.

###TCP vs UDP

UDP é um protocolo mais simples da camada de transporte. Ele não garante a entrega, preservação da sequência correta ou proteção contra
duplicação dos dados, com o objetivo de um procedimento enviar dados para outro procedimento com o menor mecanismo de protocolo possível.
Seu header só contém a porta de origem, destino e um checksum de uso opcional para a verificação de erros.


###IPv4 vs IPv6

O protocolo IPv4 foi por décadas a base para o modelo TCP/IP. Seu header é formado de 160 bits. No entanto, em 96 foi padronizado o protocolo
IPv6. O principal motivo para o seu desenvolvimento é a necessidade de mais endereços. O IPv4 usa 32 bits para especificar um endereço. Com o
crescimento explosivo da internet nos últimos anos, esse tamanho de endereço ficou insuficiente. IPv6 usa 128 bits. Além disso, existe outros
fatores como a transmissão de um packet para muitos destinos de uma só vez, a unicidade dos endereços etc.

### Bibliografia

- KUROSE, J. F. e ROSS, K. - Redes de Computadores e a Internet - 5ª Ed., Pearson, 2010.
- Wikibooks: Redes de computadores/Protocolos e serviços de rede. Disponível em: <https://pt.wikibooks.org/wiki/Redes_de_computadores/Protocolos_e_serviços_de_rede>.
