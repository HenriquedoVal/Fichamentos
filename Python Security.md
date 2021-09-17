# Python Security

## Ip simples

~~~Python
import os

ip_ou_host = input("digite o ip ou host a ser verificado: ")

os.system('ping -n 6 {}'.format(ip_ou_host))
~~~

## Ip múltiplo

Depois de criar um arquivo .txt ('hosts' por exemplo) no mesmo diretório, contendo ips, um por linha:

~~~Python
import os, time #time aqui é firula

with open('hosts.txt') as file:
    dump = file.read()
    dump = dump.splitlines()
  
    for ip in dump:
        os.system('ping '+ip)
        time.sleep(5)
~~~
 
 ## Cliente TCP
 
~~~Python
import socket, sys
 
def main():
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0) #família, tipo e protocolo
    except socket.error as e:
        print('A conexão falhou')
        print('Erro: {}'.format(e))
        sys.exit()
    
    print("socket criado com sucesso")
    host_alvo = input('digite o host ou ip a ser conectado: ')
    porta_alvo = input('digite a porta a ser conectada: ')
    
    try:
        s.connect((host_alvo, int(porta_alvo)))
        print('cliente tcp conectado com sucesso no host', host_alvo, 'e a porta', porta_alvo)
        s.shutdown(2)
    except socket.error as e:
        print('não foi possível conectar ao host', host_alvo, 'e a porta', porta_alvo)
        print('Erro: {}'.format(e))
        sys.exit()       
 
if __name__ == '__main__':
    main()
~~~

## Cliente e Server UDP

Rodar primeiro o servidor. 

1. Cliente

~~~Python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
print('Cliente socket criado com sucesso')

host = 'localhost'
porta = 5433
mensagem = 'olá servidor!'

try:
    print('Cliente: '+ mensagem)
    s.sendto(mensagem.encode(), (host, porta))
    
    dados, servidor = s.recvfrom(4096)
    dados = dados.decode()
    print('Cliente: '+ dados)
    
finally:
    print('Cliente: fechando conexão')
    s.close()
~~~

2. Servidor UDP

~~~Python
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
print('Socket criado com sucesso')

host = 'localhost'
porta = 5432

s.bind((host, porta)) # faz a ligação cliente/servidor
mensagem = '\nServidor: Olá, Cliente'

while 1:
    dados, endereço = s.recvfrom(4096)
    if dados:
        print('Servidor enviando mensagem')
        s.sendto(dados + (mensagem.encode(), end)
~~~

## Gerador de Senhas

~~~Python
import random, string

tamanho = 16

chars = string.ascii_letters + string.digits + '!@#$%&()+-*/:|'

rnd = random.SystemRandom()

print(''.join(rnd.choice(chars) for i in range(tamanho)))
~~~

Notar que funcionaria perfeitamente com o choice sem o SystemRandom, porém a implementação deste inutiliza o seed.

Doc:
>class random.SystemRandom([seed])
>Classe que usa a função os.urandom() para gerar números aleatórios a partir de fontes fornecidas pelo sistema operacional. Não disponível em >todos os sistemas. Não depende do estado do software e as sequências não são reproduzíveis. Assim, o método seed() não tem efeito e é ignorado. >Os métodos getstate() e setstate() levantam NotImplementedError se chamados.

## Comparador de Hashes

A partir de dois aquivos com textos diversos, compara seus hashes

~~~Python
import hashlib

arquivo1 = 'a.txt' #nome dos arquivos
arquivo2 = 'b.txt'

hash1 = hash2 = hashlib.new('ripemd160') # pode ser md5, sha1 etc

hash1.update(open(arquivo1,'rb').read())
hash2.update(open(arquivo2,'rb').read())

if hash1.digest() != hash2.digest():
    print(f'O arquivo {arquivo1} é diferente do arquivo {arquivo2})

# para printar o hash
print(hash1.hexdigest())
~~~

Interessante dar uma olhada no digest e hexdigest

Para comparar strings:

~~~Python
import hashlib

string = input('Digite a string a ser gerado o hash: ')
hash = hashlib.md5(string.encode('utf-8')) #sha1, sha512, sha256...
print(hash.hexdigest())
~~~

## Threads e Ips

1. Multithreading

~~~Python
from threading import Thread
import time

def carro(vel, pil):
    trajeto = 0
    while trajeto <= 100:
        print('Piloto:', pil, 'km:', trajeto)
        trajeto += vel
        time.sleep(0.8)

t_carro1 = Thread(target=carro, args=[2,'Henrique'])
t_carro2 = Thread(target=carro, args=[4,'Luciano'])

t_carro1.start()
t_carro2.start()
~~~
Não funcionou no colab. Talvez não seja possível multithread

2. IPs

~~~Python
import ipaddress

ip = '192.168.0.1'

endereco = ipaddress.ip_address(ip)
# torna mais facilitado operações com número de ip
print(endereço + 35000)

rede = '198.168.0.0/24' #24, 32, 0, 4

end_rede = ipaddress.ip_network(rede)
for ip in end_rede:
    print(ip)
~~~

# Gerador de Wordlists

Usar do módulo Itertools a função Permutations que tem como primeiro argumento os caracteres a serem permutados e o segundo, a quantidade de caracteres a serem *outputados*. Lembrar que o resultado da função é uma tupla, usar o método join numa string vazia para uma saída mais limpa

# Web Scraping

~~~Python
from bs4 import BeautifulSoup
import requests

site = requests.get('https://www.climatempo.com.br').content
soup = BeautifulSoup(site, 'html.parser')
print(soup.prettify())

'''
soup.find('tag', class_='açleihfalç') tag html como primeiro argumento, class com underline
soup.title.string   --  vai pegar o conteudo da tag como string, sem '.string' vem a tag junto
soup.a    -- primeira tag que achar como âncora
soup.p    -- primeira tag p
soup.find('qualquer coisa que vc quiser')
'''
~~~

# Web Crawler

~~~Python
import requests
import operator
from bs4 import BeautifulSoup
from collections import Counter

def start(url):
    wordlist = []
    source_code = requests.get(url).text
    soup = BeautifulSoup(source_code, 'html.parser')
    
    for each_text in soup.findAll('div', {'class':'entry-content'}):
        content = each_text.text
        words = content.lower().split()
        for each_word in words:
            wordlist.append(each_word)
        clean_wordlist(wordlist)

def clean_wordlist(wordlist):
    clean_list = []
    for word in wordlist:
        symbols = '!@#$%&*()-+={[}]?/:;>.<,|\\'
        for i in range(len(symbols)):
            word = word.replace(symbols[i], '')
        if len(word) > 0:
            clean_list.append(word)
    create_dictionary(clean_list)

def create_dictionary(clean_list):
    word_count = {}
    for word in clean_list:
        if word in word_count:
            word_count[word] += 1
        else:
            word_count[word] = 1
    for key, value in sorted(word_count.items(), key = operator.itemgetter(1)):
        print('% s : % s ' % (key, value))
    c = Counter(word_count)
    top = c.most_common(10)
    print(top)

start(url)
~~~

## Verificador IP externo

~~~Python
import json
from urllib.request import urlopen

url = 'https://www.ipinfo.io/json'
resposta = urlopen(url)
dados = json.load(resposta)
~~~

## Ferramenta gráfica para abrir o navegador

Eu sei que não tem muito a ver, a princípio, com segurança, mas segue:

~~~Python
import webbrowser
from tkinter import *

root = Tk( ) # Observar a necessidade do espaço no campo de argumento desta função

root.title('Abrir Browser')
root.geometry('300x200')

def google():
    webbrowser.open('www.google.com')
    
mygoogle = Button(root, text='Abrir o Google', command=google).pack(pady=20)
root.mainloop()
~~~
