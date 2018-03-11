---
title: HTTP — um Protocolo no Nível de Aplicação
authors:
- karl-dubost
intro: 'Este é o primeiro de uma série de artigos para ensinar o básico do HTTP e como usá-lo mais efetivamente. Neste artigo, veremos aonde o HTTP se encaixa na engrenagem da Internet. HTTP é um protocolo no nível de aplicação, feito sobre o TCP/IP, um protocolo de comunicação.'
tags:
- client
- http
- protocol
- server
- tcp-ip
language: pt
translator: Wagner Macedo
license: cc-by-3.0
---

## Introdução

Em Butão, quando as pessoas se encontram, normalmente cumprimentam-se com “Sua saúde vai bem?” No Japão, deve se inclinar em reverência a depender das circunstâncias. Em Omã, os homens geralmente dão um beijo no nariz do outro após apertar as mãos. Em Camboja e Tailândia, eles juntam as mãos como se estivessem rezando. Todos esses são protocolos de comunicação, um sequência simples de códigos com algum significado e prepara os dois lados para uma troca expressiva.

Na Web, temos um protocolo de aplicação muito efetivo que preparam os computadores pelo mundo afora para trocas expressivas: [Hypertext Transfer Protocol][1] ou HTTP. HTTP é um protocolo no nível de aplicação, feito sobre o [TCP/IP][2], um protocolo de comunicação. HTTP parece frequentemente esquecido em aulas de projeto e desenvolvimento de sites web, o que é imperdoável: esse entendimento ajudará os alunos a definir melhores interações de usuário, obter melhor performance do site e criar ferramentas efetivas para o gerenciameno de informações na Web.

[1]: https://en.wikipedia.org/wiki/HTTP
[2]: https://en.wikipedia.org/wiki/TCP/IP_model

Este é o primeiro de uma série de artigos com o intuito de ensinar o básico do HTTP e como podemos usá-lo mais efetivamente. Neste artigo, veremos aonde o HTTP se encaixa na engrenagem da Internet.

## O que é um protocolo de comunicação?

Antes de olhar os detalhes, vamos considerar um cenário de comunicação básico. Para poder se comunicar, duas partes (sejam software, dispositivos, pessoas, etc.) precisam de:

- Sintaxe (formato dos dados e do código)
- Semâtica (informações de controle e tratamento de erros)
- Tempo (correspondência da velocidade e sequenciamento)

Quando duas pessoas se encontram, elas se envolvem usando um protocolo de comunicação: por exemplo, no Japão uma pessoa fará um gesto específico com o corpo. Tal gesto é uma reverência, que é a **sintaxe** usada para a interação. Nos costumes japaneses, o gesto de reverenciar (entre outros) é associado com a **semântica** de cumprimentar alguém. Finalmente, quando uma pessoa reverencia outra, uma sequência de eventos foi estabelecida entre as duas em um dado **tempo**.

<figure block="figure">
	<img elem="media" src="{{ page.id }}/communication.png" alt="Protocolo de comunicações">
	<figcaption elem="caption">Protocolo de comunicações</figcaption>
</figure>

Um protocolo de comunicação online contém os mesmos elementos. A sintaxe será a sequência de caracteres como as palavras-chave que usamos para escrever os protocolos. A semântica é o significado associado com cada uma dessas palavras-chave e, finalmente, o tempo é a ordem em que duas ou mais entidades trocam essas palavras-chave.

## Onde o HTTP se encaixa na engrenagem?

O próprio HTTP roda sobre outros protocolos. Quando conecta-se a um site web, por exemplo em `www.example.org`, o agente do usuário está usando o conjunto de protocolos TCP/IP. O [modelo TCP/IP][4] foi desenvolvido em 1970 com [4 camadas distintas][5]:

[4]: http://en.wikipedia.org/wiki/TCP/IP_model
[5]: https://tools.ietf.org/html/rfc1122

1. **Interface de Rede** descreve o acesso à mídia física (e.g. usando uma placa de rede)
2. **Internet** descreve o invólucro e o caminho dos dados — como é empacotado (IP)
3. **Transporte** descreve a forma como os dados são entregues do ponto inicial ao destino final (TCP, UDP)
4. **Aplicação** descreve o significado ou o formato das mensagens transferidas (HTTP)

HTTP é um **protocolo de aplicação** que fica acima do protocolo de comunicação. É importante ter isso em mente. Separar o modelo em camadas independentes ajuda para evoluir as partes da plataforma sem ter que reescrever tudo. Por exemplo, TCP, um protocolo de transporte, poderia evoluir sem ter que modificar o HTTP, o protocolo de aplicação. Na prática, os detalhes se tornam um pouco desagradáveis quando estamos ambicionando comunicação de alta performance. Para os primeiros artigos de HTTP, daremos foco à separação de camadas assim como definido no modelo TCP/IP. HTTP foi definido para trocar informações entre dois softwares via mensagens HTTP. O modo como damos forma e desenhamos essas mensagens tem consequências no cliente (o browser, por exemplo), no servidor (o site web) e seus intermediários (o proxy).

## Vamos nos dirigir a um servidor

A porta 80 é a porta padrão para conectar a um servidor web. Podemos tentar isso nós mesmos usando o shell. Abra seu terminal/linha de comando e tente abrir uma conexão para `www.opera.com` na porta 80 usando o seguinte comando:

	telnet www.opera.com 80

You should get an output like so:

	Trying 195.189.143.147...
	Connected to front.opera.com.
	Escape character is '^]'.
	Connection closed by foreign host.

We can see that the terminal is trying to communicate with the server located at IP address `195.189.143.147`. If we don’t do anything else the server will close the connection by itself. It is entirely possible to use a different port and a different communication protocol, but these are the most common.

## Let’s speak a bit of HTTP

Let’s try again to communicate with the server. Enter the following message into your terminal/command line:

	telnet www.opera.com 80

Once this is done and the communication is established, type the following HTTP message quickly (before the connection automatically closes), then press enter/return twice:

	GET / HTTP/1.1
	Host: www.opera.com

This message specifies:

- `GET`: That we wish to `GET` a representation of information.
- `/`: That the information we want to get at is stored at the root of the site.
- `HTTP/1.1`: We are speaking using `HTTP` version 1.1.
- `Host:`: I’m trying to reach a specific site.
- `www.opera.com`: the name of the site is www.opera.com.

Now it is time for the server to answer. You should see the terminal window fill up with content along these lines:

	HTTP/1.1 200 OK
	Date: Wed, 23 Nov 2011 19:41:37 GMT
	Server: Apache
	Content-Type: text/html; charset=utf-8
	Set-Cookie: language=none; path=/; domain=www.opera.com; expires=Thu, 25-Aug-2011 19:41:38 GMT
	Set-Cookie: language=en; path=/; domain=.opera.com; expires=Sat, 20-Nov-2021 19:41:38 GMT
	Vary: Accept-Encoding
	Transfer-Encoding: chunked

	<!DOCTYPE html>
	<html lang="en">
	…

Here the server is saying: “I speak `HTTP` version 1.1. Your request was successful, so I have responded with the code `200`.” The `OK` string is optional and meant for explaining what this code means to humans — in this case, everything is OK and our message has been accepted. A series of `HTTP headers` are then sent to describe what the message is, and how it should be understood. Finally, the contents of the page located at the root of the site is included, beginning with `<!DOCTYPE html>`. The list of HTTP verbs and codes will be explained in the next few articles.

<figure block="figure">
	<img elem="media" src="{{ page.id }}/request-response.jpg" alt="HTTP request and response">
	<figcaption elem="caption">HTTP request and response</figcaption>
</figure>

## Summary

We just talked HTTP — it is as simple as that! We sent a message (exactly like writing a letter) and we received an answer because our message was understood. Next time we will explore in detail what some of these headers mean and what they can be used for.
