# **TCPDump**

## **Introdução**

O TCPDump é uma alternativa para a captura de tráfego de redes. Ele é uma funcionalidade nativa do Linux que possibilita, através de linhas de comando, capturar e analisar o tráfego de redes. Com essa ferramenta, é possível capturar pacotes e salvá-los em um arquivo `.pcap` para posterior análise.

## **Captura de Tráfego**

Um simples comando `tcpdump` permite a captura de tráfego. Para visualizar as interfaces de rede disponíveis, utiliza-se o parâmetro `-D`:

tcpdump \-D

Para escolher uma interface específica, utiliza-se:

tcpdump \-i \[interface\]

Exemplo:

tcpdump \-i en0

## **Aplicando Filtros**

### **Filtragem por Host**

* Filtrar por IP de origem:

tcpdump src \[IP\]

* Filtrar por IP de destino:

tcpdump dst \[IP\]

### **Filtragem por Porta**

Para filtrar por porta específica:

tcpdump dst port 25

### **Filtragem por Protocolo**

Para capturar pacotes de um protocolo específico:

tcpdump ftp

Assim como no Wireshark, operadores lógicos como `and` e `or` podem ser usados para refinar a captura. Para especificar a quantidade de pacotes capturados, usa-se `-c`.

## **Analisando Arquivos PCAP**

Para ler e salvar capturas em arquivos:

tcpdump \-r \[nome-do-arquivo\]

Para salvar o arquivo com mais detalhes, pode-se usar `-v`, `-vv` ou `-vvv`.

### **Estrutura de um Pacote TCP**

1. A primeira linha do pacote TCP exibe informações do cabeçalho IP, incluindo TTL, flags, protocolo da camada 4, tamanho e fragmentação offset.  
2. A segunda linha mostra o IP de origem e porta, o IP de destino e porta, flags TCP (`s, p, f, r, u, a`), checksum, sequence number, tamanho da janela e cabeçalho.

## **Especificando TCP Flags**

Para capturar pacotes com uma flag TCP específica:

tcp\[tcpflags\] \== tcp-ack/tcp-syn/tcp-fin/tcp-push/tcp-urg/tcp-rst

Para capturar pacotes com a flag `PSH` configurada:

tcp\[tcpflags\] \== tcp-push

### **Combinando Várias Flags TCP**

As flags TCP podem ser convertidas em valores decimais para capturas combinadas. Exemplo:

* `ACK` e `PSH`: `00011000 = 24`

tcp\[tcpflags\] \== 24

Exemplo de uso em um arquivo PCAP:

tcpdump \-r FTP.pcap "tcp\[tcpflags\] \== 24"

## **Especificação de Bytes no Cabeçalho**

Para capturar apenas pacotes de requisição `echo ICMP`, sabendo que este tipo de pacotes tem o código 8:

icmp\[0\] \== 8

Da mesma forma, pode-se aplicar filtros a outros campos, como `ip[8]` para o TTL.

## **Outros Recursos**

* `-A`: Mostra pacotes com conteúdo em ASCII  
* `-x`: Exibe dump hexadecimal do pacote  
* `-X`: Exibe todos os itens  
* `-t`, `-tt`, `-tttt`, `-ttttt`: Administram timestamps

## **Exemplos de Consultas**

1. **Quantos pacotes UDP foram capturados?**

tcpdump \-r SBT-PCAP4.pcap udp | wc \-l

**Resposta:** 3290

2. **Quantos pacotes TCP possuem as flags SYN e ACK configuradas?**

tcpdump \-r SBT-PCAP4.pcap "tcp\[tcpflags\] \== 18" | wc \-l

**Resposta:** 20

3. **Qual versão do Chrome foi usada para acessar `securityblue.team`?**

tcpdump \-v \-r capture.pcap port 80

**Resposta:** Chrome/80.0.3987.87

4. **Quantos pacotes possuem TTL igual a 38?**

sudo tcpdump \-nn \-r SBT-PCAP4.pcap 'ip\[8\] \== 38' | wc \-l

**Resposta:** 721

5. **Qual o nome do arquivo PNG no servidor 192.168.56.111?**

tcpdump \-A \-r SBT-PCAP5.pcap | grep png

**Resposta:** proprietary.png

6. **Qual versão do OpenSSH está rodando no servidor?**

tcpdump \-vv \-r SBT-PCAP5.pcap | grep OpenSSH

**Resposta:** 7.9pl

7. **Em qual porta está sendo servido o arquivo `.zip`?**

tcpdump \-A \-r SBT-PCAP5.pcap | grep .zip \-B 3

**Resposta:** 3016

8. **Quando um pacote com checksum TCP de valor 53203 foi capturado?**

08:04:58.454287  
---

