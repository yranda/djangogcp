## Aplicação Django com MySQL no GCP(Google Cloud Plataform)

### Acesso

I. Basta acessar o seguinte endereço:
   ```
   https://djangocloud-307013.rj.r.appspot.com/
   ```
II. Projeto conforme tutorial (YouTube):
   ```
      https://youtube.com/playlist?list=PL6u1VNwqZdJaFJmwhPmiEJNKlYGYWJ46U
   ```

### Anotações:

#### Aula I
- Instalação do SDK.
- Criar uma conta na google cloud.
- Testar SDK do google cloud instalado: gcloud -v.

#### Aula II
- Habilitação da conta de faturamento (feito na hora do cadastro).

#### Aula III
- Criar instância SQL.
- Menu -> SQL -> Criar instância MySQL.
- Ao criar a instância você pode escolher o ID, senha e versão do banco de dados.
- Além de escolher a disponibilidade por região e zona.

#### Aula IV
- Para logar na conta via terminal através do SDK:
  ```
    gcloud auth login
  ```
- Para escolher o projeto que será trabalhado (PROJECT_ID deve ser consultado no painel do gcp):
  ```
    gcloud config set project PROJECT_ID (djangocloud-307013)
  ```
- O ID pode ser consultado na página inicial do Google Cloud Plataform(GCP).
- As informações da instância pode ser buscadas através do comando:
  ```
    gcloud sql instances describe dbdjango (meu banco de dados)
  ```
- Serve para buscar minhas informações da instância, no caso, do dbdjango que está configurado.

#### Aula V
- O Proxy vai redirecionar todas as conexões feitas na porta local da nossa máquina para uma instância sql que esta rodando na plataforma do gcp.
- Baixar cloud_sql_proxy no seguinte endereço:
  ```
    https://github.com/GoogleCloudPlatform/cloudsql-proxy/releases
  ```
- Você precisará executar este código:
  ```
    gcloud  sql instances describe dbdjango
  ```
- A partir do código acima, você precisará da informação que está no parâmetro "connectionName", no meu caso:
  ```
    djangocloud-307013:southamerica-east1:dbdjango
  ```
- Depois vc usará o executável junto com o seguinte código (mais o parâmetro acima):
  ```
    cloud_sql_proxy_x64.exe -instances djangocloud-307013:southamerica-east1:dbdjango=tcp:3306
  ```
- Com isso ele executa localmente o banco de dados, e todas as conexões que chegarem na porta 3306 ele vai transferir para instância do google.
- Para conectar localmente no banco de dados (está parte eu realizei no linux, não no windows, mas não é necessária para que o projeto funcione).
  ```
    mysql -u root -h 127.0.0.1 -P 3306 -p
  ```
- Invés de criar o banco local e replicar no google cloud eu criei lá no google cloud.

#### Aula VI

#### Aula VII
  ```
    poetry init
    poetry install
    poetry shell (mostra o caminho onde esta o ambiente virtual)
  ```

- Para ativar no Windows basta adicionar a pasta script/active (seguido do active) ficando da forma abaixo:
  ```
    C:\Users\gusta\AppData\Local\pypoetry\Cache\virtualenvs\djangogcp-cZGLeebN-py3.9\Scripts\activate
  ```
- O comando abaixo adiciona o que queremos no ambiente:
  ```
    poetry add django (no caso, o django)
  ```
- Comando usado para iniciar o projeto django:
  ```
    django-admin startproject djangogcp .
    manage.py runserver
  ```

- Será gerado um link, onde estará rodando o projeto.

#### Aula VIII
- Nesta parte eu vou editar o arquivo settings.py, deixando a parte do banco de dados da seguinte forma:
- Vou definir engine do banco (mysql), host, user, password e o nome que foi dado ao banco de dados.

```
if os.getenv('GAE_APPLICATION', None): /*Verifica se o ambiente esta definido*/
    DATABASES = {
    	'default': {
        	'ENGINE': 'django.db.backends.mysql',
		'HOST': '/cloud/djangocloud-307013:southamerica-east1:dbdjango', /*O 'sufixo' pode ser pego através do comando: gcloud sql instances describe dbdjango*/
		'USER': 'root',
		'PASSWORD': '*****',
		'NAME': 'dbdjango',
	    }
	}
	else:
	DATABASES = {
    	'default': {
        	'ENGINE': 'django.db.backends.mysql',
		'HOST': '127.0.0.1',
		'PORT': '3306'
		'USER': 'root',
		'PASSWORD': '*****',
		'NAME': 'dbdjango',
	    }
	}
```

#### Aula IX
- Executar o migrate:
  ```
    manage.py migrate
  ```
#### Aula X

#### Aula XI
- Criação do super usuário.
- Login padrão: admin, admin.
- Comando abaixo envia as informações para um .txt:
- Preferencialmente usar o de baixo.
  ```
  pip freeze > requirements.txt
  poetry export -f requirements.txt > requirements.txt
  ```

#### Aula XII
- Arquivos são colocados na pasta static através do comando:
  ```
    manage.py collectstatic
  ```
- Realização do deploy através do comando:
  ```
    gcloud app deploy
  ```
- O comando acima também atualiza as modificações realizadas no projeto, caso seja executado de novo.

#### Aula XIII

- Nesta última aula, é adicionado as "url" no arquivo settings.py conforme abaixo:
  ```
    ALLOWED_HOSTS = ['localhost', 'djangocloud-307013.rj.r.appspot.com']
  ```

### Conclusão

Atenção a alguns erros:
- Problema no deploy do projeto foi corrigido adicionado o mysqlclient ao poetry.
  ```
  poetry add mysqlclient
  ```
- O ambiente virtual criado com poetry precisa estar ativado.
- Utilizar o comando do poetry para fazer o requirements, conforme está na aula 11.
- O proxy pode ser encontrado na página do github, conforme aula 5.


## License

Distributed under the MIT License. See `LICENSE` for more information.
