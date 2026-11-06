# Laboratório seguro: simulações de ransomware e registro de teclas

## Sobre o desafio

Este projeto explora comportamentos associados a malwares em um cenário
educacional controlado, usando Python. O objetivo é entender a criptografia de
arquivos, o registro de eventos de teclado e a geração de relatórios, relacionando
essas práticas às medidas de prevenção e defesa.

Projeto educacional em Python. Usa somente dados fictícios. A simulação de
ransomware cria cópias criptografadas de duas amostras fixas; os arquivos originais
permanecem disponíveis. A simulação de teclado exige consentimento e registra
eventos apenas enquanto o campo da própria janela tem foco.

## Funcionalidades

| Parte do desafio | Implementação |
| --- | --- |
| Arquivos de teste | Duas amostras fixas geradas pelo próprio programa |
| Criptografia | Cópias protegidas com Fernet, da biblioteca `cryptography` |
| Descriptografia | Recuperação em memória e comparação com os dados originais |
| Mensagem de resgate | Aviso explicitamente identificado como simulação |
| Registro de teclas | Eventos no campo da interface Tkinter, mediante consentimento |
| Furtividade | Adaptada para uma demonstração visível e consentida |
| E-mail automático | Geração local de `.eml` a cada 30 segundos, sem transmissão |
| Reflexão sobre defesa | Discussão de backups, antivírus, firewall e isolamento |

## Estrutura do projeto

```text
projeto/
├── README.md
├── requirements.txt
├── .gitignore
├── ransomware_simulado.py
├── keylogger_simulado.py
└── laboratorio/                 # Gerado durante a execução; ignorado pelo Git
    ├── ransomware/
    │   ├── exemplo.txt
    │   ├── tarefas.txt
    │   ├── chave.key
    │   ├── exemplo.txt.encrypted
    │   ├── tarefas.txt.encrypted
    │   └── AVISO_SIMULACAO.txt
    └── teclado/
        └── sessao_.../
            ├── teclas.txt
            └── email_simulado.eml
```

## Execução

Requisitos: Python 3.12+, pip e Tkinter (normalmente incluído no Python para Windows).
No PowerShell, dentro desta pasta:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe ransomware_simulado.py preparar
.\.venv\Scripts\python.exe ransomware_simulado.py criptografar
.\.venv\Scripts\python.exe ransomware_simulado.py descriptografar
.\.venv\Scripts\python.exe keylogger_simulado.py
```

Os resultados ficam em `laboratorio/`, ignorado pelo Git. A preparação recusa
um laboratório existente. Para repetir, arquive ou remova manualmente apenas
essa pasta após revisar seu conteúdo.

Para verificar se o Tkinter está disponível, execute `python -m tkinter`.
Uma pequena janela de demonstração deverá abrir.

## Experimentos e resultados esperados

1. `preparar` cria duas amostras de texto e uma chave Fernet local.
2. `criptografar` valida os textos contra as amostras fixas e gera arquivos
   `.encrypted`, além de `AVISO_SIMULACAO.txt`, uma mensagem fictícia de resgate.
3. `descriptografar` recupera as cópias em memória, compara os bytes com as
   amostras e imprime a confirmação. Nenhum arquivo pessoal é selecionado.
4. Na janela de teclado, marque a autorização e digite uma frase fictícia.
   O arquivo `teclas.txt` recebe eventos, com horário UTC. Eventos de tecla não
   representam necessariamente o texto final: edição e modificadores também contam.
5. A cada 30 segundos, enquanto autorizado, um e-mail MIME é salvo em
   `email_simulado.eml`. O botão também permite gerar esse arquivo. Não existe
   conexão SMTP, credencial, destinatário real ou transmissão pela rede.

O exercício de furtividade foi adaptado para observação defensiva: a janela e
o consentimento permanecem visíveis. Não há captura global, persistência,
ocultação ou inicialização automática. Revogar o consentimento interrompe novos
registros e a geração periódica; os arquivos já criados permanecem locais.

## Validação

O ciclo de preparação, criptografia e descriptografia foi executado com sucesso
no ambiente de desenvolvimento, com Python 3.12.1. A recuperação confirmou o
conteúdo das duas amostras:

```text
exemplo.txt: descriptografado e validado: Arquivo sintetico para estudo de seguranca.
tarefas.txt: descriptografado e validado: Estudar backups e autenticacao multifator.
```

Os dois scripts passaram pela verificação de sintaxe com `python -m py_compile`.
A interface gráfica ainda precisa de validação manual. Para realizá-la:

1. Abra a interface e digite texto fictício sem marcar a autorização: o log deve
   permanecer vazio.
2. Marque a autorização, clique no campo e digite outra frase: confira o log local.
3. Aguarde 30 segundos e confira a criação de `email_simulado.eml` na sessão.
4. Desmarque a autorização e confirme que novos eventos deixam de ser registrados.
5. Feche a janela para encerrar a aplicação.

O botão de geração de e-mail funciona manualmente, inclusive com o registro
desativado, e usa o conteúdo já existente no log. Cada geração atualiza o mesmo
arquivo `.eml` da sessão.

## Dificuldades comuns

| Situação | Como resolver |
| --- | --- |
| `ModuleNotFoundError: cryptography` | Instale `requirements.txt` com o mesmo Python usado para executar o script. |
| Preparação informa que a pasta já existe | O laboratório já foi criado. Continue a sequência ou arquive a pasta `laboratorio/ransomware` antes de recomeçar. |
| Arquivo `.encrypted` já existe | Execute a descriptografia para validar o resultado existente. |
| Amostra modificada | O programa aceita apenas os textos fictícios originais. Prepare um novo laboratório para repetir o experimento. |
| Tkinter indisponível | Use uma instalação do Python que inclua suporte a Tk/Tcl. |

## Aprendizados e defesa

- **Criptografia:** Fernet oferece criptografia autenticada. Perder a chave impede
  recuperar as cópias; nesta demonstração ela fica ao lado delas por conveniência.
  Uma implantação real deve separar e proteger suas chaves. Criptografia legítima
  e ransomware podem usar técnicas semelhantes; autorização e impacto diferem.
- **Backups:** mantenha cópias separadas e teste a restauração. Uma cópia sempre
  acessível ao mesmo usuário pode também ser afetada por um incidente.
- **Antivírus e monitoramento:** ajudam a identificar executáveis suspeitos e
  alterações incomuns. Nenhuma ferramenta isolada garante proteção.
- **Firewall:** restrinja conexões de saída quando necessário e monitore tráfego
  inesperado. Ele não impede sozinho a criptografia de arquivos locais.
- **Sandboxing e privilégios:** experimente em máquina virtual com dados fictícios,
  sem pastas pessoais compartilhadas e com uma conta sem privilégios administrativos.
  Este código limita os destinos, mas não constitui uma sandbox de segurança.
- **Conscientização:** verifique anexos, fontes de downloads e pedidos de credenciais;
  mantenha o sistema atualizado e use autenticação multifator.
- **Resposta:** diante de um incidente real, isole o equipamento da rede e acione a
  equipe responsável. Preserve evidências e siga o procedimento da organização.

## Compartilhamento

Publique estes fontes e este README em um repositório de sua escolha. Inclua
capturas com frases fictícias e a saída de validação; não publique chaves, logs ou
dados pessoais. A pasta de resultados e o ambiente virtual estão no `.gitignore`.
Este projeto prepara o material para compartilhamento; não publica automaticamente
em contas externas.
