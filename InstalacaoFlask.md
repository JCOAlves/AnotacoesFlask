# **1. Instalação do Flask**
Antes de instalar o Flask, é altamente recomendável criar um ambiente virtual. Isso isola as dependências do seu projeto, evitando conflitos com outros projetos Python no seu sistema.

## **1. Criando um Ambiente Virtual**
1. **Abra o terminal ou prompt de comando.**
2. **Navegue até o diretório onde você quer criar seu projeto.**
    
    ```bash
    cd /caminho/para/seu/projeto
    ```
    
3. **Crie o ambiente virtual:**
    ```bash
    python -m venv .venv
    ```
    
(Onde `.venv` é o nome do seu ambiente virtual. Você pode escolher outro nome se preferir.)

## 2. Ativando o Ambiente Virtual
Após criar o ambiente virtual, você precisa ativá-lo:

- **No Windows:**
    ```bash
    .venv\Scripts\activate
    ```
    
- **No macOS/Linux:**
    ```bash
    source venv/bin/activate
    ```
    

Você saberá que o ambiente virtual está ativo quando o nome do ambiente (por exemplo, `(.venv)`) aparecer no início da linha de comando.

## **3. Instalando o Flask**
Com o ambiente virtual ativado, você pode instalar o Flask usando `pip`:

```bash
pip install Flask
```

Para saber se o Flask foi instalado com sucessso, utilize os comandos:`pip show Flask` ou `Flask —version`
