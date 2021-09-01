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

Criar ambos programas em mesmo nível, rodar primeiro o servidor. 

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
