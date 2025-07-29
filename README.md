# API de Produtos Inteligente com N8n e IA Generativa

Este projeto demonstra a criação de uma API inteligente, construída na plataforma de automação N8n e potencializada por um Modelo de Linguagem Grande (LLM) da Groq. O objetivo central é interpretar pedidos de produtos feitos em linguagem natural e retornar uma lista de itens correspondentes de uma base de dados de 1500 produtos.

## Para Que Serve

A API foi projetada para atuar como um backend para sistemas de vendas, chatbots, ou qualquer aplicação que precise traduzir um texto livre de um cliente (ex: "quero 1 açaí e 2 coca 2L") em uma lista estruturada de produtos pesquisáveis, simulando um entendimento humano do pedido.

## Principais Funcionalidades

* **Processamento de Linguagem Natural:** A API recebe um texto livre e é capaz de compreender o que está sendo solicitado.
* **Extração de Entidades:** Identifica de forma inteligente os produtos e as quantidades desejadas no texto, mesmo que não estejam perfeitamente formatados.
* **Busca em Base de Dados:** Realiza uma busca determinística em uma base de dados interna (simulando 15.000+ produtos) para encontrar os itens que correspondem aos termos extraídos.
* **Saída JSON Padronizada:** Retorna os resultados em um formato JSON limpo e previsível, ideal para ser consumido por outras aplicações.

## Como Funciona: A Arquitetura Final

Após um processo iterativo de desenvolvimento e depuração, o projeto chegou a uma arquitetura final robusta que se baseia no princípio da **Separação de Responsabilidades**, garantindo eficiência, escalabilidade e precisão.

O fluxo de trabalho segue os seguintes passos:

1.  **Webhook (Entrada):** A API expõe um endpoint de webhook que aguarda uma requisição `POST` contendo o texto do pedido em um corpo JSON.

2.  **IA Generativa - LLM (O Parser Inteligente):** O texto recebido é enviado para o LLM (Groq). A **única responsabilidade** da IA nesta etapa é "traduzir" a linguagem humana em dados estruturados. Ela não busca produtos, apenas extrai os termos de busca e suas quantidades. Por exemplo, transforma `"32 moletom preto"` em `[{"termo": "moletom preto", "qtde": 32}]`.

3.  **Nó de Código - JavaScript (O Motor de Busca):** Este é o coração da lógica de busca. Ele recebe os termos estruturados da IA e executa as seguintes ações:
    * Carrega a base de dados completa de produtos.
    * Para cada termo recebido da IA, ele aplica uma lógica de filtro determinística e eficiente para encontrar os produtos correspondentes na base de dados.
    * Ele consolida todos os resultados encontrados e formata a saída no padrão final desejado.

4.  **Resposta (Saída Estruturada):** O resultado da busca é retornado como resposta da chamada de API.

Essa arquitetura evita os problemas comuns de LLMs, como "alucinações" e limites de tokens, usando a IA apenas para sua especialidade (linguagem) e deixando a busca de dados para um código determinístico e confiável.

## Tecnologias Utilizadas

* **N8n:** Plataforma de automação de fluxo de trabalho que orquestra todo o processo.
* **Groq:** Fornecedor da API de LLM, utilizado para o processamento de linguagem natural em alta velocidade.
* **JavaScript (Node.js):** Utilizado nos nós de código do N8n para implementar a lógica de busca, formatação e as regras de negócio.
* **Docker:** Ambiente utilizado para hospedar e executar a instância do N8n.

## Como Usar

1.  **Endpoint:** `http://<seu-n8n-host>/webhook/produtos`
2.  **Método:** `POST`
3.  **Headers:** `Content-Type: application/json`
4.  **Body (Exemplo):**
    ```json
    {
      "texto": "32 moletom preto, 12 fanta laranja"
    }
    ```

**Exemplo de Comando `curl`:**

```bash
curl -X POST http://localhost:5678/webhook/produtos \
-H "Content-Type: application/json" \
-d '{"texto": "2 Agua, 2 fanta laranja"}'

```
## Desafios e Aprendizados
O desenvolvimento deste projeto foi uma jornada de resolução de problemas e otimização de arquitetura.

Limites de Token da IA: A abordagem inicial de enviar toda a base de dados para a IA provou-se inviável, resultando em erros de "Request too large". Isso forçou a busca por uma solução mais inteligente.

Inconsistência da Resposta (Alucinação): Em testes iniciais, a IA por vezes retornava dados não relacionados ou falhava em encontrar correspondências óbvias, demonstrando que não era confiável para a tarefa de busca.

A Importância da Arquitetura: O principal aprendizado foi a necessidade de desenhar uma arquitetura com clara separação de responsabilidades. Isolar a IA na tarefa de parsing de linguagem e usar código determinístico para a busca de dados foi a chave para criar um sistema funcional, eficiente e confiável.




