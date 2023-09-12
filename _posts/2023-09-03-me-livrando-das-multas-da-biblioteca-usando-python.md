---
layout: post
title: "Me livrando das multas da biblioteca usando python"
date: 2023-09-03 17:00:00 -0300
---
Eu confesso. Tem coisas que esqueço muito facilmente e é por isso que, se eu não configurar alguns lembretes no calendário, as datas passam voando por mim.

Acontece que certos esquecimentos doem no bolso, então minhas contas ficam no débito automático e assim minha vida funciona.

Porém, ainda tinha uma conta que toda semana me causava alguns reais a menos e eu ainda não tinha parado pra resolver isso: a conta da biblioteca.

Embora fosse tão fácil simplesmente entrar no aplicativo, ir na lista de livros emprestados, verificar o que já tinha passado do prazo e clicar no botão "renovar", eu só lembrava de fazer isso lá pelas duas horas da madrugada quando acordava de um sonho em que a bibliotecária confiscava a minha casa e prendia a minha família pois eu esqueci de devolver o livro de Cálculo na semana passada.

Então, ao invés de gastar alguns minutos configurando lembretes semanais para os livros que peguei, eu resolvi gastar algumas horas fazendo um script que renova todos os livros pra mim :)

## O algorítmo

Em resumo, são necessários 4 passos:

1. Fazer o login na api do portal acadêmico
2. Armazenar o `access_token` para ser usado nas próximas requisições
3. Fazer a requisição que lista os livros que peguei emprestados
4. Verificar a data de devolução de cada livro, e
5. Caso seja a data de devolução, fazer a requisição que renova o livro


## A api

#### O login

Para descobrir as urls da api do portal acadêmico, usei o DevTools do meu navegador. Então fiz o login e descobri que a requisição para fazer login é um POST para `/apiapp/login2`:

```
POST /apiapp/login2 HTTP/1.1
Host: portal.multivix.edu.br
```
No body da requisição são enviados meu login e senha.

```json
{
	"username": "my-user",
	"password": "my-pass-123"
}
```

#### A lista de livros

Seguindo na descoberta das urls, fui até a página dos livros emprestados da biblioteca e vi que é um POST para `/apiapp/api/biblioteca/listaemprestimorenovavel`. O que recebo de resposta é um dicionário contendo os livros em três categorias: renováveis, não renováveis e renovados.

```json
{
	"renovaveis": [
		{
			"codigo": 3751947,
			"ttl_nome": "Física I: mecânica",
			"autor": "YOUNG, Hugh D.",
			"mda_nome": "Livros",
			"dtemprestimo": "2023-09-03T00:00:00",
			"dtdevolucaoestimada": "2023-09-11T00:00:00",
			"renovado": true
		},
		{
			"codigo": 3749846,
			"ttl_nome": "Cálculo",
			"autor": "STEWART, James",
			"mda_nome": "Livros",
			"dtemprestimo": "2023-08-30T00:00:00",
			"dtdevolucaoestimada": "2023-09-06T00:00:00",
			"renovado": true
		}
	],
	"naorenovaveis": [
		{
			"codigo": 0,
			"ttl_nome": "Variáveis complexas e aplicações 3ed., 2013",
			"autor": "ÁVILA, Geraldo",
			"mda_nome": "Livros",
			"dtemprestimo": "2023-09-05T00:00:00",
			"dtdevolucaoestimada": "2023-09-12T00:00:00",
			"renovado": false
		}
	],
	"renovados": [
		{
			"codigo": 0,
			"ttl_nome": "Variáveis complexas e aplicações 3ed., 2013",
			"autor": "ÁVILA, Geraldo",
			"mda_nome": "Livros",
			"dtemprestimo": "2023-09-05T00:00:00",
			"dtdevolucaoestimada": "2023-09-12T00:00:00",
			"renovado": false
		}
	]
}
```

#### A renovação do livro

Tendo um livro disponível para renovação, cliquei em renovar e então vi que a requisição para isso é um POST para `/apiapp/api/biblioteca/renovaobra` com o código do livro no body.


## O script

Bom, como falei lá em cima, de forma resumida o script deve logar, listar os livros, verificar a data de devolução de cada livro e renovar, caso esteja na data de devolução. O processo principal será assim:

```python
# main.py

def process(self):
	borrowed_books = self.api.list_borrowed_books()

	for book in borrowed_books:
		self.renew_if_is_due_date(book)
```

O primeiro método usado é o que lista os livros renováveis:

```python
# api/library_apy

def list_borrowed_books(self):
        path = f'/api/biblioteca/listaemprestimorenovavel'

        try:
            response = self.request('POST', path, headers=headers)

            if response.status_code == 200:
                content = json.loads(response.text)
                borrowed_books = content.get('renovaveis')
                return borrowed_books
        except Exception as e:
            print(e)
```

E então, com os livros em mãos - ou melhor, em códigos -, percorro a lista de livros verificando se é necessário renoválo:

```python
# main.py

def renew_if_is_due_date(self, book: dict):
	due_date = self.get_due_date(book)

	if self.now > due_date:
		book_code = book['codigo']
		self.api.renew_book(book_code)


def get_due_date(self, book: dict):
	unformatted_due_date = book.get('dtdevolucaoestimada')
	due_date = datetime.strptime(unformatted_due_date, '%Y-%m-%dT%H:%M:%S')
	return due_date
```

E o método `renew_book()` da api faz apenas um POST contendo o código do livro a ser renovado:

```python
# api/library_apy

def renew_book(self, book_code):
	path = f'/api/biblioteca/renovaobra'

	body = {
		'codigo': book_code
	}

	try:
		response = self.request('POST', path, body=body)
		return response
	except Exception as e:
		print(e)
```

Aqui mostrei os passos principais do script de uma forma superficial. Ao final do post disponibilizo o link do github, caso você queira olhar com mais detalhes toda a estrutura do código.

## A hospedagem

Para a hospedagem do meu código, resolvi usar o Lambda AWS com um gatilho para todar todo dia às 00:10h, uma vez que as datas de renovação são sempre às 00:00h.
Olhando nos logs, consigo verificar que está tudo rodando corretamente e eu não vou mais receber multas da biblioteca, hehe:

![Logs do script rodando e renovando o livro de Cálculo](imgs/log-cloug-watch.png)


---------------------

Bom, caso você queira olhar com mais detalhes o funcionamento do script, pode checar o repositório: https://github.com/Mr-Saxobeat/renew-my-borrowed-books