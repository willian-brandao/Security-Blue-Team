# **Wireshark**

O Wireshark é um software popular usado por diversos especialistas de redes para capturar e analisar tráfego de rede em detalhes. Existem diversos parâmetros, estatísticas e informações detalhadas disponíveis nos tráfegos que podem ser capturados. O usuário visualiza o tráfego de rede, desde os bytes individuais de um pacote único até um apanhado estatístico de protocolos dentro da captura.

## **Elementos do Wireshark**

### **Barra de Menu**

A barra de menu permite iniciar, pausar e parar a captura, além de manipular as configurações de interface.

### **Filtros de Busca**

Os filtros são usados para buscar pacotes específicos. Pode-se construir uma expressão especificando os campos principais e opcionais com os valores desejados. Também é possível usar operadores lógicos para encadear as expressões.

**Exemplo de filtro:**

ip.src\_host \== 192.168.1.7 and tcp.port \== 443 and ssl.record.version \== 0x0303

Explicação:

* O primeiro parâmetro filtra pacotes com IP de origem 192.168.1.7.  
* O segundo parâmetro filtra pacotes com origem ou destino na porta TCP 443 (SSL/TLS).  
* O terceiro parâmetro filtra pacotes que utilizam TLS versão 1.2.  
* Os três parâmetros estão combinados com "AND", exigindo que todos sejam satisfeitos para o pacote ser exibido.

### **Paineis Principais**

A janela principal do Wireshark possui três paineis:

1. **Lista de Pacotes** \- Exibe as principais informações dos pacotes capturados, como número do pacote, tempo, IPs de origem e destino, protocolos, tamanho e resumo dos cabeçalhos.  
2. **Detalhes do Pacote** \- Apresenta informações detalhadas sobre o pacote selecionado.  
3. **Representação Hexadecimal e ASCII** \- Exibe os bytes do pacote em formato hexadecimal e ASCII.

## **Exemplos de Filtros**

* `tcp.window_size_value >= 8000` \- Mostra pacotes TCP com tamanho de janela maior ou igual a 8000 bytes.  
* `tcp.port == 80` \- Exibe pacotes TCP na porta 80 (HTTP).  
* `ip.dst_host == 192.168.118.100 && tcp` \- Filtra pacotes TCP destinados ao IP 192.168.118.100.  
* `ntp || udp.port == 2000` \- Exibe pacotes do protocolo NTP ou pacotes UDP na porta 2000\.

# **Relatório de Análise**

Analisando um arquivo PCAP, é possível buscar dados que identifiquem atividades maliciosas em uma rede. Utilizando o Wireshark, foi possível extrair informações de acessos a um servidor FTP e um servidor web, além do download de um arquivo malicioso.

### **Protocolo usado na porta 3942**

A análise foi realizada com o filtro:

tcp.port \== 3942 || udp.port \== 3942

O protocolo identificado foi o **SSDP**.

### **IP do host que recebeu ping duas vezes**

A análise de pacotes ICMP revelou seis requisições, duas das quais foram recusadas. O host que recebeu as requisições foi **8.8.4.4**.

### **Número de respostas de consulta DNS capturadas**

Foram analisados pacotes DNS e filtradas as respostas com:

dns.flags.response \== 1

Outro filtro possível para consultas DNS bem-sucedidas:

dns.response\_in

### **Host que enviou o maior número de bytes**

Usando **Estatísticas \> Endpoints**, o host que enviou mais pacotes foi **115.178.9.18**.

### **Senha do WebAdmin**

Investigando requisições HTTP, encontramos uma requisição GET para um arquivo .txt contendo credenciais em texto plano:

password: sbt123

### **Versão do servidor FTP do invasor**

Analisando as requisições FTP, encontramos o nome e versão do servidor: **1.5.5**.

### **Porta usada para acessar o host Windows da vítima**

Usando:

ip.src \== 192.168.56.1

Identificamos pacotes **ACK** indicando conexão estabelecida na porta **8081**.

### **Nome de um arquivo confidencial no host Windows**

Analisando o tráfego TCP, identificamos o arquivo:

Employee\_Information\_CONFIDENTIAL.txt

### **Nome do arquivo de log criado às 4:51 AM**

Utilizando **Follow TCP Stream**, encontramos o arquivo:

LogFile.log

## **Filtragem de Pacotes HTTP e HTTPS**

### **Se o WebAdmin usa HTTP:**

http

### **Se usa HTTPS:**

tls

Caso o TLS esteja mal configurado, é possível tentar capturar a chave de descriptografia no Wireshark:

1. **Editar \> Preferências \> Protocolos \> TLS**  
2. Adicionar o arquivo de chave privada do servidor.

### **Encontrando Requisições de Login**

Para encontrar **POST requests**:

http.request.method \== "POST"

Para autenticação **Basic Authentication**, utilize:

http.authbasic

A senha estará codificada em Base64, podendo ser facilmente decodificada.

## **Filtragem por Timestamp**

1. Filtrar pacotes criados às 4:51 AM:

frame.time \== "04:51:00"

2. Filtrar tráfego SMB (porta 445):

smb2 || smb && frame.time \== "04:51:00"

3. Filtrar tráfego FTP (porta 21):

ftp && frame.time \== "04:51:00"

4. Buscar arquivos de log comuns:

\*.log, \*.txt, \*.evtx, \*.csv  
---

