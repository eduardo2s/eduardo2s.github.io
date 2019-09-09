---
layout: post
title: Extrair informações de uma tabela em um documento .DOCx
date: 2019-09-09 11:08:00 +0800
description: Extrair informações de uma tabela em um documento .DOCx # Add post description (optional)
img: word.jpg # Add image post (optional)
tags: [python, pandas, word, docx] # add tag
---
Nesse post irei demonstrar como extrair informações de uma tabela dentro de um documento .docx

Antes de começar, a imagem abaixo demonstra como uma tabela dentro de um documento do word é distribuida, isso irá ajudar a estruturar o código.

![Tabeladentrodoword](https://eduardo2s.github.io/assets/img/tabela.jpg)

#### Importando

{% highlight python %}
from docx.api import Document
import win32com.client as win32
from time import sleep #define um periodo de tempo entre uma ação e outra
from tqdm import tqdm #adiciona uma barra de "loading"
import glob
import os
  {% endhighlight %}

Para trabalhar com esse tipo de documentos iremos utilizar a lib win32 e a lib docx, que auxiliam no trabalho com documentos word, nomeadamente com a extensão .docx, o restante das livrarias importadas irão auxiliar de diferentes formas no código.

#### Parte incial

Nessa etapa iremos criar uma lista chamada "data" que terá o número das linhas que queremos extrair da tabela que foi mostrada na figura do inicio, fazemos também a chamada da lib win32, que irá permitir que os documentos sejam abertos em segundo plano, assim quando o script iniciar o word não irá abrir o documento e caso aconteça de abrir o script irá parar, fazemos isso porque caso estejamos fazendo o processo para muitos documentos a abertura de todos eles irá deixar o computador lento e possivelmente irá inviabilizar o script.

{% highlight python %}
data = [2,3]
final_list = [ ]
word = win32.gencache.EnsureDispatch('Word.Application')
word.Visible = 0
path = 'C:\\Users\\user\\Desktop\\folder' #iremos extrair informações de documentos dentro da pasta "folder" 
  {% endhighlight %}

#### Coletando as informações

A seguir temos a parte principal do código, onde a mágica aconetece, no caso eu fiz uma determinada utilização para o tipo de tabela que aparece no documento, entretanto, é possível efetuar mudanças e acredito que existem formas mais interessantes de estruturar o código.

{% highlight python %}
for infile in tqdm(glob.glob(os.path.join(path, '*.docx') ), desc="processando dados", unit="files"):
	document = Document(infile)
	doc = word.Documents.Open(infile)
    if doc.Tables.Count == 2:
        table = doc.Tables(1)
        table2 = doc.Tables(2) #supondo que um documento tenha duas tabelas
        linhas = table.Rows.Count + table2.Rows.Count #faz a contagem de linhas nas tabelas
        for i in range(1 , linhas):
            listateste = []
            listateste.append(table.Cell(Row = data[0], Column = 1).Range.Text.rstrip('\r\x07'))
            listateste.append(table.Cell(Row = data[0], Column = 2).Range.Text.rstrip('\r\x07'))
            listateste.append(table.Cell(Row = data[0], Column = 3).Range.Text.rstrip('\r\x07'))
            listateste.append(table.Cell(Row = data[0], Column = 4).Range.Text.rstrip('\r\x07'))
            listateste.append(table.Cell(Row = data[1], Column = 1).Range.Text.rstrip('\r\x07'))
        final_list.append(listateste)
    doc.Close(False)
word.Quit()s
  {% endhighlight %}

Existem alguns pontos que devem ser levados em consideração:
1 - a lista "data" colocamos o número da linha da tabela exatamente como ela aparece no documento word, ou seja, começamos coletando da linha 2, adicionamos como primeiro item da lista "data" o número 2.

2 - O python começa sua contagem a partir do 0, sendo assim como a lista "data" vai ser utilizada posteriormente, o primeiro item da lista (2) está na posição (0), e isso irá se refletir na hora da extração.

3 - listateste.append(table.Cell(Row = **data[0]**, Column = 1).Range.Text.rstrip('\r\x07')), nessa linha de código estamos extraindo os dados da linha 2, na coluna 1.

4 - caso tivessemos duas ou mais tabelas dentro do documento o código mudaria de:
listateste.append(**table.Cell**(Row = **data[0]**, Column = 1).Range.Text.rstrip('\r\x07')) para
listateste.append(**table2.Cell**(Row = **data[0]**, Column = 1).Range.Text.rstrip('\r\x07'))

Levando isso em consideração, ao finalizar a coleta é possível colocar os dados em um arquivo excel/csv.