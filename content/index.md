---
title: "Guia ChatBot GTHC"
draft: false
tags: [time, docs]
description: "Resumo do funcionamento do nosso projeto"
---

# 🤖 Bem-vindo ao Guia Completo do Projeto ChatBot - GTHC

Seja bem-vindo ao **chatbot**, seu guia completo para entender e explorar todas as funcionalidades do nosso projeto de chatbot inteligente!  

---

## 🔹 Sobre o Projeto

O **chatbot - GTHC** é uma aplicação projetada para **interagir de forma natural com usuários**, automatizando respostas, oferecendo suporte e criando experiências eficientes e inteligentes.  
Nosso objetivo é entregar **um chatbot versátil e fácil de usar**, para os alunos da UFABC com conteúdos sobre a Universidade, buscando dinamizar o acesso a informação e conteúdos diversos

---

Dentro do **chatbot**, a estrutura está organizada em **4 subitens principais**: as pastas `app`, `src`, `shared` e uma série de **9 arquivos de instrução**.  

O chatbot funciona como um **sistema integrado entre frontend, backend e motor RAG**:

1. O usuário envia uma mensagem pelo frontend (`index.html` + `script.js`).  
2. A mensagem é recebida pelo backend em **Flask**.  
3. O backend utiliza funções do `src/` para **consultar o índice vetorial dos documentos** e gerar respostas com base no conteúdo disponível.  
4. O motor RAG combina **recuperação de informações relevantes** com **geração de texto pelo modelo de linguagem**, garantindo respostas precisas e contextualizadas.  
5. A resposta é enviada de volta ao frontend e exibida ao usuário.

> Toda a infraestrutura de dados, incluindo carregamento de documentos, criação de índices e consultas, está centralizada na pasta `src`. A pasta `shared` define os **modelos de dados** usados na comunicação. O script de execução garante que as dependências estejam instaladas e que o servidor Flask rode de forma organizada e prática.

---

## 🔹 Mais detalhes sobre seu funcionamento 

Dentro do ChatBot do GTHC temos uma estrutura dividida em 4 subitens principais, sendo elas a pasta *app*, *src*, *shared*, e por fim, uma série de arquivos de instrução, sendo eles 9 no total. 

O chatbot funciona como um sistema integrado entre **frontend, backend e motor RAG**. O usuário envia uma mensagem pelo frontend (index.html + script.js), que é recebida pelo backend em Flask. O backend utiliza funções do `src/` para consultar o índice vetorial dos documentos e gerar respostas com base no conteúdo disponível. O motor RAG combina **recuperação de informações relevantes** com **geração de texto pelo modelo de linguagem**, garantindo respostas precisas e contextualizadas. Finalmente, a resposta é enviada de volta ao frontend e exibida ao usuário. 

Toda a infraestrutura de dados, incluindo carregamento de documentos, criação de índices e consulta, está centralizada na pasta `src`, enquanto a pasta `shared` define os modelos de dados usados na comunicação. 

O script de execução garante que dependências estejam instaladas e que o servidor Flask rode de forma fácil e organizada.

## Subpasta App
Dentro da subpasta app temos uma série de outras 2 subpastas *app* e *src* e um arquivo __init__.py o qual serve apenas para identificar a pasta app como um pacote python, ele sempre estará presente em pastas com conteúdo em python e tem as seguintes funções: ^init-py

- Garantir que o Python reconheça `app/app/` como um pacote.
- Facilitar imports de módulos do app em outros lugares do projeto.
- É uma prática padrão em projetos Python, mesmo que não tenha código dentro.
### `App/app`
Dentro do App/App temos as subpastas Statics e Template e os arquivos app.py, requirements.txt e run.py ⟶ Essa subpasta é o **núcleo da aplicação web** do chatbot. Ela concentra tudo que é necessário para rodar o backend e disponibilizar a interface do chat ao usuário.
#### `Statics`
pasta `Static` **serve para armazenar arquivos que o navegador do usuário pode baixar diretamente**, sem precisar de processamento do backend. Isso inclui:
- **JS (JavaScript)** → interação e lógica do front
- **CSS** → aparência/estilo da página
- **Imagens, fontes, ícones** → elementos visuais

Nesse projeto temos o *script.js*, *style.css* e *backup.js* onde o **`backup.js`** está vazio, mas temos ainda: 
##### `script.js`
- Este sim é **responsável pela interatividade da interface do chat** e contém:
    - Captura do que o usuário digita no input do chat
    - Envio da mensagem para o backend via `fetch` ou `XMLHttpRequest`
    - Recebimento da resposta do bot e atualização da página (DOM) dinamicamente
    - Event listeners para botões e envio de mensagens ao apertar Enter
- Em outras palavras, **é o cérebro do front para a interação do usuário com o chatbot**.

##### `style.css`
- Contém **as regras de estilo** da interface: cores, fontes, tamanhos, margens, layout do chat.
- Tudo que faz a interface “ficar bonita” ou organizada vem daqui e não tem lógica de envio de mensagens, só aparência.

#### `Template`
Em *template* nós temos apenas um arquivo `index.html` cujo o qual contém um HTML dinâmico que é responsável apenas pela estrutura/base visual do front-end do chatbot. Ele só define a estrutura: campos de input, botões, caixas de chat, layout etc.

Em conjunto com o `script.js`, e o `style.css` o `index.html` forma o front-end do chatbot, onde respectivamente são responsáveis por:
- Coordenar a interação dinâmica entre o usuário (que apenas tem contato com o front-end) e o back-end do chatbot.
- Estilizar a página com elementos visuais harmônicos e estéticamente bem apresentáveis.
- Estruturar a página (front-end) como um todo, organizando sua base e suas estruturas 

#### `app.py`

Aqui está a principal lógica por traz do funcionamento do chatbot, portanto vou passar bloco por bloco para ficar bem didático.
##### **Resumo do fluxo geral**
Abaixo segue um resumo da lógica central de funcionamento, e posteriormente, todas as suas etapas bem explicadas!

1. Servidor Flask inicia com `run.py`.
2. Carrega configurações do modelo RAG e embeddings.
3. Verifica se existe um índice vetorial dos documentos; se não, cria um novo.
4. Frontend (`index.html` + `script.js`) envia mensagens para rota `/chat`.
5. Backend (`generate_response`) usa RAG para gerar respostas.
6. Resposta volta para o frontend e é mostrada ao usuário.
##### **1 Ambientação** 
Aqui vamos iniciar abordando os imports necessários e variáveis de ambiente, estamos puxando todo conteúdo necessário para desenvolver o código. A começar pelos **imports e bibliotecas utilizadas** temos: (As sua funções estão comentadas no bloco de código):
###### **Imports internos** 

```python
from flask import Flask, request, jsonify, render_template
#Flask → framework web em Python para criar o backend.
#request → pega dados da requisição (ex.: mensagens do usuário).
#jsonify → retorna respostas JSON ao frontend.
#render_template → renderiza templates HTML (`index.html`).

from flask_cors import CORS
#CORS → Cross-Origin Resource Sharing, permite que o frontend em outro domínio/porta acesse o backend.

from llama_index.embeddings.huggingface import HuggingFaceEmbedding
#HuggingFaceEmbedding → modelo de embeddings (representações vetoriais de texto).

from dotenv import load_dotenv
#load_dotenv → carrega variáveis do arquivo `.env` (como API keys).

import os
import sys
from pathlib import Path
# os, sys, Path → manipulação de arquivos, caminhos e variáveis de ambiente.
```

```python
from llama_index.llms.groq import Groq
#Groq → modelo de LLM hospedado na API da Groq.

from src.rag.ingestion import (
    default_path_index,
    create_vector_index,
    get_documents,
    get_vector_index,
)
#src.rag.ingestion → funções para **ingestão de documentos** e criação do índice vetorial.

from src.rag.querying import create_engine
#src.rag.querying → função `create_engine` para criar **motor de busca** RAG.

from llama_index.core import Settings
#Settings → configurações globais do LLM e embeddings.
```

###### **Adicionar diretório pai ao PYTHONPATH**

```python
sys.path.append(str(Path(__file__).parent.parent))
load_dotenv()
GROQ_API_KEY = os.getenv("GROQ_API_KEY")
```

- Esse bloco permite importar módulos que estão fora da pasta atual. Carrega `.env` para pegar a **chave da API do modelo Groq**.
###### **Variáveis de ambiente**

```python
os.environ["TOKENIZERS_PARALLELISM"] = "false"
os.environ["CUDA_VISIBLE_DEVICES"] = "-1"
```

- `TOKENIZERS_PARALLELISM=false` → evita que o modelo use vários processos ao mesmo tempo para "quebrar texto em tokens". Isso não atrapalha o funcionamento, só tira mensagens de aviso desnecessárias no terminal.
- `CUDA_VISIBLE_DEVICES=-1` → diz para o Python **ignorar a GPU** e rodar tudo só na CPU. Isso garante que o app funcione mesmo em máquinas sem placa de vídeo.


##### **2 Inicialização do Flask**
Flask é um framework web em Python, ou seja, um conjunto de ferramentas que facilita a criação de aplicações web. Ele permite definir rotas, como `/chat` ou `/health`, receber requisições do usuário, como mensagens do chat, e enviar respostas de volta em JSON ou HTML. Basicamente, Flask transforma seu código Python em um servidor web capaz de se comunicar com o frontend.

```python
app = Flask(__name__)
CORS(app)
```
- Cria a aplicação Flask.
- `CORS(app)` → permite requisições de outros domínios (frontend separado do backend).

##### **3 Configuração do modelo**
A configuração do modelo define quais modelos serão usados pelo chatbot para gerar respostas e processar informações. O `Settings.llm` especifica o modelo de linguagem (LLM), suas instruções de comportamento e o nível de criatividade das respostas, enquanto o `Settings.embed_model` define o modelo de embeddings, responsável por transformar textos em vetores numéricos que capturam o significado das palavras. Essa configuração garante que o sistema consiga tanto **entender o conteúdo das perguntas do usuário** quanto gerar respostas coerentes e contextualizadas.

```python
def setup_settings():
    Settings.llm = Groq(
        model="meta-llama/llama-4-maverick-17b-128e-instruct", 
        api_key=GROQ_API_KEY, 
        temperature=0.4,
        system_prompt="Sempre formate suas respostas em HTML..."
    )
    Settings.embed_model = HuggingFaceEmbedding(
        model_name="intfloat/multilingual-e5-large", 
        embed_batch_size=4,
        device="cpu"
    )
```

- `Settings.llm` → define qual modelo de linguagem será usado.
- `temperature=0.4` → controla criatividade/resposta do modelo (0 = muito preciso, 1 = mais aleatório).
- `system_prompt` → instruções para o modelo, nesse caso: sempre responder em HTML, estilo informal de chat e formatação em listas e código quando apropriado
- `Settings.embed_model` → modelo de **embeddings multilingue**, usado para RAG (Recuperação + Geração). É o modelo que transforma textos em números para que o chatbot consiga entender semântica e buscar informações relevantes antes de gerar a resposta.
- `device="cpu"` → roda na CPU.

##### **4 Inicialização do sistema RAG**
A inicialização do sistema RAG (Retrieval-Augmented Generation) prepara o motor de busca do chatbot, combinando recuperação de informações e geração de texto. Nessa etapa, o sistema verifica se existe um índice vetorial dos documentos; caso não exista, cria e persiste um novo índice. Em seguida, o motor de busca é criado usando esse índice, permitindo que o chatbot consulte os documentos relevantes antes de gerar respostas, garantindo respostas mais precisas e baseadas no conteúdo disponível.

```python
def initialize_rag():
    if not default_path_index.exists():
        documents = get_documents()
        index = create_vector_index(documents=documents)
        index.storage_context.persist(persist_dir=default_path_index)
    
    if default_path_index.exists():
        index = get_vector_index(path_folder=default_path_index)
    
    engine = create_engine(index=index)
    return engine
```

**RAG** = **Retrieval-Augmented Generation** → combina busca em documentos + geração de texto do modelo. Segue os seguintes passos:
1. Se **não houver índice vetorial salvo**: Pega documentos (`get_documents`), cria índice vetorial (`create_vector_index`) e salva (`persist`) para uso futuro
2. Se o índice existe, só carrega (`get_vector_index`).
3. Cria motor de busca (`create_engine`) → usa índice para responder perguntas do usuário.        
##### **5 Função de resposta**
A função de resposta recebe o prompt enviado pelo usuário e utiliza o motor RAG para gerar uma resposta baseada nos documentos disponíveis. Ela processa o resultado, limpando formatação e espaços extras para garantir que a resposta fique organizada. Caso ocorra algum erro durante a geração, a função retorna uma mensagem amigável, mantendo a experiência do usuário consistente.

```python
def generate_response(prompt):
    try:
        response = engine.query(prompt)
        clean_response = str(response)
        return clean_response.strip()
    except Exception as e:
        return f"⚠️ **Erro**: {str(e)}"
```
##### **6 Rotas do Flask**
As rotas do Flask definem os caminhos pelos quais o frontend se comunica com o backend. Cada rota corresponde a uma URL específica e determina como o servidor deve responder. No chatbot, por exemplo, a rota `/` carrega a página principal (`index.html`), a rota `/chat` recebe mensagens do usuário, processa via motor RAG e retorna a resposta, e a rota `/health` fornece informações de status do servidor, como uso de CPU e memória. Dessa forma, as rotas organizam e controlam todas as interações entre usuário e aplicação.

```python
@app.route('/')
def index():
    return render_template('index.html')
```
Renderiza o **HTML base** do frontend (`templates/index.html`).

```python
@app.route('/chat', methods=['POST'])
def chat():
    try:
        data = request.json
        user_message = data.get('message', '')
        
        if not user_message.strip():
            return jsonify({'error': 'Mensagem vazia'}), 400
        
        bot_response = generate_response(user_message)
        
        return jsonify({'response': bot_response, 'status': 'success'})
    
    except Exception as e:
        return jsonify({'error': f'Erro interno do servidor: {str(e)}', 'status': 'error'}), 500
```
Recebe **mensagens do frontend** via POST, depois valida que não está vazia. Chama `generate_response()` para obter a resposta do bot. E por fim retorna **JSON** com a resposta

```python
@app.route('/health')
def health_check():
    import psutil
    return jsonify({
        'status': 'healthy',
        'cpu_usage': psutil.cpu_percent(),
        'memory_usage': psutil.virtual_memory().percent
    })
```
Rota de verificação do servidor. Retorna **status, uso de CPU e memória**, útil para monitoramento.

##### **7 Rodando o servidor**
Essa parte do código é responsável por iniciar o servidor web do chatbot. Quando executado, o Flask “abre” uma porta (neste caso, a 5001) e fica aguardando requisições do frontend. O parâmetro `debug=True` permite que o servidor atualize automaticamente sempre que o código for alterado e mostre mensagens detalhadas de erro. O `host='0.0.0.0'` faz com que o servidor seja acessível a partir de qualquer endereço IP da máquina, não apenas `localhost`.

```python
if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5001)
```

#### `requirements.txt`
É **uma lista de dependências do projeto**,  ou seja, todas as bibliotecas que o seu app precisa para rodar corretamente. Cada linha normalmente tem o **nome da biblioteca** e a **versão específica**, no formato: `nome_da_biblioteca==versão` 
Isso garante **consistência**: se alguém baixar o projeto, vai instalar exatamente as mesmas versões que nós utilizamos, evitando conflitos ou bugs.

#### `run.py`
Esse script é um ponto de execução do chatbot que integra o Flask com o sistema RAG. Ele faz duas coisas principais: Garante que todas as dependências do projeto estejam instaladas (`install_requirements`). Inicializa o servidor Flask, exibindo mensagens amigáveis e cuidando de erros (`run_flask_app`). Ele serve como um atalho “tudo-em-um para rodar o chatbot sem precisar digitar vários comandos no terminal. Esse script funciona como atalho único para rodar o chatbot:

1. Verifica se todas as bibliotecas do projeto estão instaladas.
2. Confirma que o `app.py` existe.
3. Exibe mensagens informativas sobre inicialização.
4. Executa o Flask para disponibilizar o chatbot na web.
5. Trata interrupções e erros de forma amigável para o usuário.



### `App/src/rag` 
Dentro do App/src temos a subpasta Rag e um arquivo [[#^init-py]]  ⟶ A pasta contém a lógica de suporte do sistema RAG, separada do Flask. Ela não lida diretamente com rotas ou frontend; em vez disso, cuida da ingestão de dados, ou seja, coleta de documentos, criação de índices vetoriais, persistência no disco (`ingestion.py`) e consultas e geração de respostas, ou seja, criação do motor de busca que o backend usa para responder perguntas (`querying.py`). ^src-rag
##### `ingestion.py`
O arquivo `ingestion.py` é responsável por carregar, processar e organizar os documentos que o chatbot vai utilizar. Ele fornece funções para ler arquivos de um diretório, transformar o conteúdo em objetos de documento (`Document`), criar um índice vetorial a partir desses documentos e também carregar índices previamente salvos. Esse índice vetorial é fundamental para o sistema RAG, pois permite que o chatbot recupere informações relevantes de forma rápida e eficiente antes de gerar respostas.
##### `querying.py`
O arquivo querying.py é responsável por criar e gerenciar o motor de busca do sistema RAG, permitindo que o chatbot consulte o índice vetorial e recupere informações relevantes antes de gerar respostas. A função create_engine transforma um índice vetorial em um motor de busca, configurando um retriever para buscar os documentos mais similares e um postprocessor para filtrar resultados com base em um limite de similaridade, garantindo maior precisão nas respostas.

O arquivo também oferece funções auxiliares para melhorar a qualidade das consultas. A função clarify_question reformula a pergunta do usuário, adicionando contexto e tornando-a mais clara para o motor de busca. A função query_with_context combina esses passos, clarifica a pergunta e realiza a consulta no motor, retornando a resposta gerada.

Em resumo, querying.py conecta o índice vetorial criado em ingestion.py com o backend do chatbot, estruturando e filtrando consultas para que o sistema RAG possa gerar respostas mais precisas e contextualizadas.


## Subpasta Shared

A pasta shared contém apenas 2 arquivos sendo eles um [[#^init-py]] e um **models.py** ⟶ O arquivo models define modelos de dados usados na comunicação do chatbot, utilizando a biblioteca Pydantic para validação e tipagem. 

O modelo ChatRequest representa a estrutura da mensagem enviada pelo usuário, incluindo o prompt e um identificador opcional de conversa. Já o modelo ChatResponse define a estrutura da resposta retornada pelo chatbot, contendo o texto da resposta e uma lista de fontes relacionadas, caso existam. 

Esses modelos ajudam a garantir que os dados trocados entre frontend e backend tenham o formato esperado, facilitando o tratamento de mensagens e respostas no sistema.



## Subpasta Src
A pasta **`src`** (source) reúne os módulos centrais de dados e inteligência do chatbot, funcionando como a base que alimenta o aplicativo; nela ficam componentes como o **`banco_de_dados`**, responsável pelo armazenamento e gerenciamento das informações; o **`rag`**, que implementa o método de **Retrieval-Augmented Generation** para conectar o modelo ao banco e garantir respostas contextualizadas; o **`scrap-dados`**, que contém scripts de coleta automatizada; e o **`scrapy`**, que utiliza o framework de web scraping para capturar e estruturar dados externos, assegurando que o chatbot tenha informações atualizadas, organizadas e acessíveis para gerar respostas inteligentes. 
### `Src/banco_de_dados`
Por hora possui apenas um arquivo `.gitkeep` de marcador já que a pasta esta vazia. Por padrão, o Git não versiona pastas vazias. Então, quando você quer manter uma pasta no repositório mesmo sem arquivos ainda, você cria esse `.gitkeep`. ^gitkeep
### `Src/rag`
Aqui você deve ter reparado que  que essa pasta [[#^src-rag]] também está presente na pasta app, mas note que elas possuem uma diferença: 

Temos dois níveis de RAG (Retrieval-Augmented Generation), um nível “global" (a `src/` geral), que cuida da infraestrutura de dados e define como o RAG funciona de forma centralizada; E um nível “aplicação” (a `src/` dentro de `app/`), que adapta esse RAG ao fluxo específico do chatbot, ou seja, como ele vai consultar e usar os dados dentro da conversa.

Ambas as pastas possuem arquivos igualmente nomeados ingestion e querying, com funções similares em diferentes níveis

Os arquivos `ingestion.py` acabam sendo iguais porque o processo de ingestão de dados (que envolve limpar, transformar e indexar informações em um vetor ou banco) é padronizado em qualquer aplicação que utiliza RAG, além de precisar ser executado apenas uma vez para popular o índice, mantendo a mesma lógica. 

Já os arquivos `querying.py` diferem porque, no nível global (`src/rag/querying.py`), eles definem uma forma genérica de consulta ao banco de vetores sem considerar o contexto da aplicação, enquanto no nível da aplicação (`app/src/rag/querying.py`) adaptam essas consultas às necessidades específicas do chatbot, como aplicar filtros, tratar o prompt, ajustar o formato da resposta ou escolher quantos documentos retornar. 

Em resumo, o `ingestion.py` é igual porque o processo de indexação não depende da aplicação, mas o `querying.py` varia justamente para se adequar ao modo como cada app consulta e utiliza os dados.
### `Src/scrap-dados
A pasta `scrap-dados` reúne os componentes responsáveis por coletar e organizar informações externas para alimentar o chatbot, dentro de **`scrap-dados`** temos uma série de subpastas sendo elas as chamadas `links_download`, `paginas_txt`, `pdfs_baixados`, `pipe`, `txt_salvos` e dois arquivos chamados `html_to_txt.py`  e `main.py`. Onde nas pastas `pdfs_baixados`, `links_download` e `txt_salvos` possuímos apenas arquivos [[#^gitkeep]]. 

#### `paginas_txt
A pasta possui apenas um [[#^gitkeep]] e um arquivo de texto com o conteúdo do Green Team Hacker Club que parece ser uma página institucional, descrevendo quem são, áreas de atuação, atividades etc. Esse texto vai ser processado pelo ingestion e buscado pelo querying caso alguma pergunta solicite essas informações. 
#### `pipe`
Tem a função de organizar a etapa de coleta e transformação de dados que o chatbot vai usar depois. O nome _pipe_ sugere que ela é uma espécie de pipeline (sequência de passos) que pega dados crus da internet (links, PDFs, HTML) e transforma em arquivos `.txt` prontos para serem usados no treinamento ou consulta do chatbot. Nele temos os seguintes arquivos: 
##### `csv_links.py`
Esse script acessa páginas da web e procura dentro delas todos os links que terminam com .pdf. Quando encontra, ele organiza esses links e os salva em um arquivo CSV. Ou seja, ele automatiza a coleta de links de PDFs a partir de uma lista de URLs.
##### `gthc_scrap.py`
Esse script lê o arquivo CSV que foi gerado no passo anterior e baixa todos os PDFs que estão listados lá. Ele acessa cada link e salva o PDF em uma pasta específica. Assim, ele é responsável por transformar os links em arquivos de fato armazenados localmente.
##### `html_to_txt_bonito.py`
Esse script faz algo parecido com o de PDF, mas para páginas HTML. Ele acessa uma lista de páginas, procura a parte principal do conteúdo (por exemplo, dentro da tag <main.>), e extrai apenas os títulos e parágrafos. Depois salva em arquivos .txt. A ideia é limpar a página e guardar só o conteúdo útil para estudo ou processamento pelo chatbot.
##### `pdf_to_txt.py`
Aqui acontece a conversão dos PDFs baixados em arquivos de texto. Ele abre cada PDF, percorre página por página e extrai o texto, salvando em arquivos .txt. Isso serve porque o chatbot precisa do conteúdo em texto puro para poder usar os dados.

#### `html_to_txt.py`
Esse script tem como objetivo extrair o conteúdo textual principal de páginas web e salvar esse conteúdo em arquivos `.txt`. Ele funciona em três etapas principais: primeiro, acessa a página da web usando o `requests` e lê o HTML; depois, utiliza o `BeautifulSoup` para remover todo o código desnecessário, como `<script>` e `<style>`, ficando apenas com o texto visível para o usuário; por fim, limpa espaços extras e transforma todo o conteúdo em um texto contínuo, pronto para ser salvo.

Na segunda etapa, o script cria um arquivo `.txt` para cada página processada, nomeando o arquivo de forma organizada com base no endereço da URL, e salvando dentro da pasta `src/scrap-dados/paginas_txt`. Ele ainda permite processar várias URLs de uma vez, extraindo e salvando cada uma de forma automática. Em resumo, esse script é uma ferramenta prática para transformar sites em textos simples, úteis para leitura, análise ou para alimentar sistemas que precisam de conteúdo textual, como chatbots.
#### `main.py`
Esse script automatiza o processo de coleta e transformação de documentos PDF em texto. Ele começa processando uma lista de URLs para encontrar todos os links de PDF disponíveis nessas páginas, salvando-os em um arquivo CSV. Em seguida, ele baixa todos os PDFs listados nesse CSV para uma pasta específica no computador. Por fim, os PDFs baixados são convertidos em arquivos de texto (`.txt`) e salvos em outra pasta, tornando o conteúdo dos PDFs facilmente acessível e manipulável.

Além disso, o script possui uma função para limpar os arquivos de texto, removendo caracteres especiais e convertendo todo o conteúdo para letras minúsculas. Isso deixa os textos padronizados e prontos para análise ou processamento em outras ferramentas, como chatbots ou sistemas de busca. Em resumo, o script cria um fluxo completo que vai desde a captura dos PDFs na web até a produção de arquivos de texto limpos e organizados.

### `Src/scrapy

Essa pasta tem 2 scripts onde primeiro script (`sacrappy-colacao`)usa Scrapy, uma ferramenta de web scraping, para extrair dados do site de colação de grau da UFABC. Ele define uma classe chamada ColacaoSpider, que começa acessando a página principal. Dentro da função parse, ele pega todo o conteúdo dentro da tag main, limpa o texto e salva em formato de dicionário, com a data e hora como chave, que será posteriormente armazenado em um arquivo JSON. Além disso, o script procura por links que terminam com .pdf e envia novas requisições para baixá-los, usando a função save_pdf. Essa função cria a pasta data/pdfs caso não exista, salva o PDF com o nome original e exibe uma mensagem de log informando que o arquivo foi salvo. Por fim, o script configura o Scrapy para salvar o texto extraído em result.json, define o nível de log como INFO e executa o spider.

O segundo script (`sacrappy-pdf`) trabalha com os PDFs que foram baixados pelo primeiro script e converte seu conteúdo em arquivos de texto. Ele lista todos os PDFs na pasta data/pdfs, abre cada arquivo com a biblioteca PyPDF2, lê todas as páginas e extrai o texto, armazenando-o em uma lista. Depois, salva o conteúdo de cada PDF em um arquivo .txt dentro da pasta data/database, usando o mesmo nome do PDF original. O fluxo geral é que o primeiro script acessa a página, extrai o texto do site e baixa os PDFs, enquanto o segundo script pega esses PDFs e gera arquivos de texto correspondentes para facilitar manipulação ou análise posterior.

## Arquivos de instrução

Por fim temos os 9 arquivos de instrução que definem as dependências necessárias para o deploy do arquivo, aquilo que ele deve ignorar, entre outros.