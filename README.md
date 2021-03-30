# Construindo-meu-primeiro-Dockerfile
Criando um dockerfile que contenha um projeto em HTML renderizando um Hello-World

Passo a passo para criar um dockerfile que contenha um projeto em HTML renderizando um Hello-World hospendando no webserver nginx: 

1 - Defini uma imagem oficial como base para que futuramente possa ser 
modificada caso precise, e escolhi um editor de texto
FROM nginx

2 - Defini as informações necessarias para a imagem como versão, descrição e autor/ou responsável 
LABEL version="1.0.0" description="Disponibilizando site com nginx" maintainer="Mateus Barcelos<mateubarcelos24@gmail.com>"

3 - Criei um diretorio no caso do linux para que possa enviar os arquivos
carregados pelo site

RUN cd / && mkdir Arquivos && chmod 777 -R Arquivos/
 1 - navegamos pelo diretorio cd /
 2 - criamos um diretorio chamado Arquivos
 3 - Damos permissão total de leitura e escrita para chmod 777 -R Arquivos/
 
4 - Copiei o site para imagem

Por padrão o nginx armazena seus itens no seguinte caminho:
/usr/share/nginx/html, sendo que é dentro desse caminho (path)
que trocamos pelo index.html de nosso site para dentro da imagem,
usando o comando COPY ./index.html /usr/share/nginx/html/

5 - Mapiei um diretorio na qual serão salvas as imagens do site
 VOLUME /Arquivos/
feito isso a imagem pegará o diretorio criado e montará o volume para ela.

6 - Defini em qual porta o servidor nginx vai rodar
EXPOSE 80

A palavra expose não faz o mapeamento de porta ela apenas deixa claro
que uma determinada porta pode ser mapeada durante a criação de
um conteiner utilizando essa imagem

7 - Configurar umas variaveis de ambiente para que possa definir um valor
padrão para elas e caso não seja informado na criação do conteiner, o valor
das variaveis serão os mesmos definidos no Dockerfile

8 - Defini qual será o diretorio de trabalho da imagem (diretorio que irá 
conter o site)
WORKDIR /usr/share/nginx/html/
É muito importante definir o diretorio de trabalho de nosso conteiner, ela será o diretorio que direcionará quando acessarmos o nosso container.

9 - subimos o nginx depois de finalizar todos os passos acima, deixando o site no ar, sendo definidas todas as regras no Dockerfile. Para finalmente o servidor entrar em operação devemos aplicar a instrução ENTRYPOINT que vai dizer onde deve ser executado o CMD ou seja um complementa o outro. O comando
final para subir vai ser /usr/sbin/nginx -g daemon off;

ENTRYPOINT ["/usr/sbin/nginx"]
CMD ["-g", "daemon off;"]

o conteúdo do index.html será:

<html>
  <head>
    <title></title>
  </head>
  <body>
      Hello-World!
  </body>
</html>   
 
 10 - esse comando cria a imagem à partir do Dockerfile
 docker build -t mabarcelos95/meu-site  .
 
 obs.: o -t serve para passar como parametro seguido do nome da imagem que queremos criar /meu-site e o . é onde se localiza o Dockerfile

saída do comando sudo docker build -t mabarcelos95/meu-site .
sudo docker build -t mabarcelos95/meu-site .
Sending build context to Docker daemon   2.56kB
Step 1/11 : FROM nginx
 ---> b8cf2cbeabb9
Step 2/11 : LABEL version="1.0.0" description="Disponibilizando site com NGINX" maintainer="Mateus Barcelos<mateubarcelos24@gmail.com>"
 ---> Using cache
 ---> 9a71de51768d
Step 3/11 : Run cd / && mkdir Arquivos && chmod 777 -R Arquivos/
 ---> Using cache
 ---> 22ff9f171761
Step 4/11 : COPY ./index.html /usr/share/nginx/html/
 ---> 4fa83ef4c77f
Step 5/11 : VOLUME /Arquivos/
 ---> Running in 5c0150e2dcfe
Removing intermediate container 5c0150e2dcfe
 ---> a9e1abdcc792
Step 6/11 : EXPOSE 80
 ---> Running in 01c6e6be72d6
Removing intermediate container 01c6e6be72d6
 ---> 0e7b82fd830c
Step 7/11 : ENV API_URL=http://localhost:8000/api/
 ---> Running in 16ef1aad3db9
Removing intermediate container 16ef1aad3db9
 ---> 06a53d07ec4f
Step 8/11 : ENV API_BANCO=meu_site
 ---> Running in c09e79c30bd2
Removing intermediate container c09e79c30bd2
 ---> 3d35ea45b92e
Step 9/11 : WORKDIR /usr/share/nginx/html/
 ---> Running in a1be91c3a044
Removing intermediate container a1be91c3a044
 ---> 5ec39fb2e549
Step 10/11 : ENTRYPOINT ["/usr/sbin/nginx"]
 ---> Running in f0cc5f862e91
Removing intermediate container f0cc5f862e91
 ---> 47aba5856b91
Step 11/11 : CMD ["-g", "daemon off;"]
 ---> Running in 9dab7f54aa8e
Removing intermediate container 9dab7f54aa8e
 ---> 9a1ba4611f6c
Successfully built 9a1ba4611f6c
Successfully tagged mabarcelos95/meu-site:latest

 11 - Depois de rodar o comando build e ocorrer tudo certo, uma imagem com nome mabarcelos95/meusite irá ser criada, e com isso podemos
 criar nosso container e subir o nosso site com este comando:
 docker run -d -p 80:80 --name meu-site -e API_URL=http://api.meusite.com.br/api/ -v $PWD/site/arquivos:/Arquivos/ mabarcelos95/meu-site
 
      
