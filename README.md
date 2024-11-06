# desafio_dio_stepfunction
Repositório para entrega de projeto
Consegui aprender bastante sobre o stepfunction da AWS porém ele não tem apresenta um bom comportamento ao tentar vincular as feature do Bedrock AWS.
Fui fazer algumas pesquisas e os modelos de IA não estão tendo cota para suporte e realização das tarefas do curso aparendo este erro
[Too many requests, please wait before trying again. You have sent too many requests.  Wait before trying again. (Service: BedrockRuntime, Status Code: 429, Request ID: 905662bf-c062-45be-ae7b-cca29ad04b52)]

Tentei fazer por outras regiões e até mesmo por outros modelo LLM diferente do Haiku, porém sem sucesso.

Achei este comentário em fórum

https://www.reddit.com/r/aws/comments/1gb2zx2/amazon_bedrock_prompt_management_error_too_many/

Não gostaria de ser prejudicado na minha formação por um mal funcionamento da plataforma.

aqui estão exemplos de indisponibilidade do modelo Haiku em duas regiões distintas da AWS

![image](https://github.com/user-attachments/assets/38a6116c-4f99-4636-8ba5-ed978860401e)

![image](https://github.com/user-attachments/assets/1d406d12-879b-4c98-afb7-0fbf80a9a2de)

Aqui deixo a evidência que não estão disponíveis os modelos na Região Oregon como foi utilizado nas aulas da DIO.

![image](https://github.com/user-attachments/assets/2011fc5e-ef7d-422a-be2a-8dceae80c801)

Desta forma colo aqui a seguir o código do progresso até onde consegui chegar

{
  "Comment": "An example of using Bedrock to chain prompts and their responses together.",
  "StartAt": "Invoke model with first prompt",
  "States": {
    "Invoke model with first prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-west-2::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 200,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "string"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Add first result to conversation history",
      "ResultPath": "$.result_one",
      "ResultSelector": {
        "result_one.$": "$.Body.generations[0].text"
      }
    },
    "Add first result to conversation history": {
      "Type": "Pass",
      "Next": "Invoke model with second prompt",
      "Parameters": {
        "convo_one.$": "States.Format('{}\n{}', $.prompt_one, $.result_one.result_one)"
      },
      "ResultPath": "$.convo_one"
    },
    "Invoke model with second prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-west-2::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 200,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "string"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "Next": "Add second result to conversation history",
      "ResultSelector": {
        "result_two.$": "$.Body.generations[0].text"
      },
      "ResultPath": "$.result_two"
    },
    "Add second result to conversation history": {
      "Type": "Pass",
      "Next": "Invoke model with third prompt",
      "Parameters": {
        "convo_two.$": "States.Format('{}\n{}\n{}', $.convo_one.convo_one, $.prompt_two, $.result_two.result_two)"
      },
      "ResultPath": "$.convo_two"
    },
    "Invoke model with third prompt": {
      "Type": "Task",
      "Resource": "arn:aws:states:::bedrock:invokeModel",
      "Parameters": {
        "ModelId": "arn:aws:bedrock:us-west-2::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "Body": {
          "anthropic_version": "bedrock-2023-05-31",
          "max_tokens": 200,
          "messages": [
            {
              "role": "user",
              "content": [
                {
                  "type": "text",
                  "text": "string"
                }
              ]
            }
          ]
        },
        "ContentType": "application/json",
        "Accept": "*/*"
      },
      "End": true,
      "ResultSelector": {
        "result_three.$": "$.Body.generations[0].text"
      }
    }
  }
}
