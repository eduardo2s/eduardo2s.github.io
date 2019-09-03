---
layout: post
title: Remover linhas duplicadas com o Pandas!
date: 2019-09-03 09:17:00 +0800
description: Remover linhas duplicadas com o Pandas! # Add post description (optional)
img: pandas.jpg # Add image post (optional)
tags: [python, pandas, código, iniciante] # add tag
---
Nesse post irei demonstrar como remover linhas duplicadas em um dataset do pandas, esse post também serve como um lembrete quando tiver a necessidade de utilizar esse código.

#### Importando

{% highlight python %}
import pandas as pd
  {% endhighlight %}

#### Criando dados ou importando uma planilha

{% highlight python %}
#criando dados
data = {"Name": ["James", "Alice", "Phil", "James"],
		"Age": [24, 28, 40, 24],
		"Sex": ["Male", "Female", "Male", "Male"]}
df = pd.DataFrame(data)
print(df)

#importando um arquivo excel ou csv
df = pd.read_csv('caminho para o arquivo',encoding='utf-8', delimiter=',')
  {% endhighlight %}
| Name   |      Age      |  Sex |
|----------|:-------------:|------:|
| James |  24 | Male |
| Alice |    28   |   Female |
| Phil | 40 |    Male |
| James | 24 |    Male |

#### Removendo os itens duplicados

para remover os itens duplicados, utilizamos a função do pandas drop_duplicates.

{% highlight python %}
df = df.drop_duplicates()
print(df)
  {% endhighlight %}
| Name   |      Age      |  Sex |
|----------|:-------------:|------:|
| James |  24 | Male |
| Alice |    28   |   Female |
| Phil | 40 |    Male |

como é possível perceber removemos a última linha que era uma duplicata da primeira linha, além disso é possível definir outros parametros para remover os dados baseado em uma única coluna, no código abaixo iremos utilizar esses parametros de forma a agrupar os dados pela idade de maior para menor, e iremos manter apenas a primeira ocorrência da linha que está duplicada.

{% highlight python %}
df = df.sort_values('Age', ascending=False)
df = df.drop_duplicates(subset='Name', keep='first')
print(df)
  {% endhighlight %}
| Name   |      Age      |  Sex |
|----------|:-------------:|------:|
| Phil |  40 | Male |
| Alice |    28   |   Female |
| James | 25 |    Male |

Assim temos o nosso problema de dados duplicados resolvido.