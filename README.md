# 🤖 Atividade Reconhecimento Facial com AWS Rekognition

Este projeto demonstra como utilizar o **Amazon Rekognition**, **AWS Lambda** e **S3** para realizar **detecção de rostos em imagens**, com foco educacional para a turma **EDN-Turma-BRSAO179-IA-AWS**, do programa AWS re/Start promovido pela **AWS** e **Escola da Nuvem**.

---
## ⚠️ Avisos antes de começar

- Verifique periodicamente o [AWS Billing Dashboard](https://console.aws.amazon.com/billing/home) para garantir que você não está gerando custos inesperados.
- Remova os recursos ao final da atividade.
- **Nunca compartilhe prints com IDs de conta, IPs privados ou informações sensíveis.**
---
## 📚 Objetivo

Capacitar os alunos a compreender e aplicar conceitos de Inteligência Artificial com serviços gerenciados da AWS, por meio da construção de um projeto prático que utiliza **Reconhecimento Facial** com **Python e Lambda**, abordando:

- Como funciona o Amazon Rekognition
- Como criar e configurar uma função Lambda
- Como armazenar imagens no S3
- Como visualizar os resultados no CloudWatch

---

## 🔍 O que é o Amazon Rekognition?

O **Amazon Rekognition** é um serviço de visão computacional da AWS que permite detectar, analisar e comparar rostos em imagens e vídeos. Ele pode identificar:

- Quantidade de rostos
- Emoções (feliz, triste, etc.)
- Faixa etária estimada
- Atributos como óculos, olhos fechados, sorrisos, etc.

Tudo isso sem a necessidade de treinar modelos manualmente — é IA pronta para uso!
---
## ✅ Pré-requisitos

- Conta AWS ativa
- Acesso ao console AWS (IAM, Lambda, S3)
- Conhecimento básico em Python
- AWS CLI instalado e configurado (opcional, mas recomendado)

---

## 🛠️ Passo a Passo

### 1. Criar o Bucket S3
1️⃣. Acesse o serviço **Amazon S3**

2️⃣. Clique em **"Criar bucket"**

3️⃣. Dê um nome exclusivo (ex: `rekognition-alunos-[seunome]`)

4️⃣. Desmarque **"Bloquear acesso público"** (para teste) (⚠️ para produção, mantenha ativado)


#### 📸 Print esperado: Bucket criado com o nome visível
![image - 2025-05-24T182412 972](https://github.com/user-attachments/assets/825fdafa-b829-490c-a8ca-20e0b303293e)

![image - 2025-05-24T182652 643](https://github.com/user-attachments/assets/e5950c84-b827-4229-ba8b-0c0962246df4)

---
### 2. Fazer Upload de Imagens
1️⃣. Faça upload de 1 ou mais imagens no bucket

2️⃣. Crie uma pasta dentro do bucket chamada uploads/ e envie as imagens para lá

📸 Print esperado: Pasta uploads/ com imagens

![image - 2025-05-24T183118 706](https://github.com/user-attachments/assets/8c08dee7-557d-44a4-8d9d-d9c7170a40da)

![image - 2025-05-24T183225 585](https://github.com/user-attachments/assets/573bfeae-1613-4320-86a5-42ce428f5165)

---

### 3. Criar Role IAM para o Lambda
1️⃣. Acesse **IAM > Funções (Roles)** → Criar Função

2️⃣. Selecione **"AWS service" > Lambda**

3️⃣. Em **Policies (Permissões)**, anexe:
   - `AmazonRekognitionFullAccess`
   - `AmazonS3FullAccess`
   - `CloudWatchFullAccess`
     
4️⃣. Dê um nome para a função (ex: `LambdaRekognitionRole`)

 📸 Print esperado: Tela da função Lambda criada

![image - 2025-05-24T184949 047](https://github.com/user-attachments/assets/2e1af931-52dd-4130-aa6c-16b3cfffdce0)

![image - 2025-05-24T185339 543](https://github.com/user-attachments/assets/2b667266-9a15-4052-a849-6281bc86be0f)

---
### 4. Criar a Função Lambda
1️⃣. Vá até **AWS Lambda > Criar função**

2️⃣. Nome: `reconhecimento-facial`

3️⃣. Runtime: `Python 3.13`

4️⃣. Role de execução: selecione a criada anteriormente

5️⃣. Clique em **"Criar função"**

📸 Print esperado: Tela da função Lambda criada

![image - 2025-05-24T185842 557](https://github.com/user-attachments/assets/9501deea-2a3a-4b94-979a-647867ce8c33)

![image - 2025-05-24T190018 260](https://github.com/user-attachments/assets/180aea51-307a-4928-a667-1d2a4af5c3d2)

![image - 2025-05-24T190116 573](https://github.com/user-attachments/assets/04eee075-129c-483f-b159-f4112096f525)

---
### 5. Inserir o Código na Função Lambda

Substitua `<nome-do-bucket>` e `<nome-da-imagem>` pelos seus dados.

```python
import boto3
import json

def lambda_handler(event, context):
    client = boto3.client('rekognition')
    
    response = client.detect_faces(
        Image={'S3Object': {'Bucket': '<nome-do-bucket>', 'Name': '<nome-da-imagem>'}},
        Attributes=['ALL']
    )
    
    face_details = response['FaceDetails']
    print(f"Total de rostos detectados: {len(face_details)}")
    
    for i, face in enumerate(face_details):
        print(f"Rosto {i + 1}:")
        print(f" - Idade aproximada: {face['AgeRange']['Low']} - {face['AgeRange']['High']}")
        print(f" - Sorrindo: {face['Smile']['Value']}")
        print(f" - Emoção principal: {face['Emotions'][0]['Type']}")
        
    return {
        'statusCode': 200,
        'body': f'Detectado(s) {len(face_details)} rosto(s).'
    }
```
📸 Print esperado: Código colado e salvo na função Lambda

![image - 2025-05-24T192127 881](https://github.com/user-attachments/assets/948ca6ab-8632-4907-9f64-f045d8ae5900)
---
### 6. Testar a Função

1️⃣. Clique em Deploy

2️⃣. Clique em Test → Crie um novo evento de teste com nome `TestFace` Deixe o conteúdo padrão `{}` e clique em Save.

3️⃣. Execute e veja a saída no painel abaixo

📸 Print esperado: Resultado exibindo as labels detectadas (ex: “Person”, “Glasses”...)

![image - 2025-05-24T191404 891](https://github.com/user-attachments/assets/040d4ec7-7e8b-4fd0-aa60-d61ac4e46491)

![image - 2025-05-24T192451 632](https://github.com/user-attachments/assets/bf729235-6e5a-401d-af52-706a1d7c39a1)

---
### 7. Acompanhar Logs no CloudWatch
Acesse: AWS CloudWatch

1️⃣. Vá até Logs > Grupos de logs

2️⃣. Clique no grupo correspondente à sua função Lambda

3️⃣. Verifique a execução e as labels no log

📸 Print sugerido: logs mostrando labels e confiança

![image - 2025-05-24T192802 680](https://github.com/user-attachments/assets/815a3c77-946f-4e43-8746-85c9a50297a0)
---
### 🧹 Limpeza dos Recursos (Clean Up)
Para evitar cobranças desnecessárias na sua conta AWS, recomenda-se excluir todos os recursos criados durante esta atividade:

Remover imagem do S3:

 - Acesse o S3 pelo console AWS.

 - Vá até o bucket `rekognition-alunos-[seunome]`

 - Exclua a imagem uploads/rosto.jpeg.

Excluir função Lambda:

 - Acesse o AWS Lambda.

 - Selecione a função criada `LambdaRekognitionRole`

 - Clique em Actions > Delete e confirme.

Excluir bucket (opcional):

 - Acesse o S3.

 - Selecione o bucket `rekognition-alunos-[seunome]`

 - Esvazie o bucket e depois clique em Delete bucket.
---

### ✅ Conclusão
Parabéns! 🎉 Ao final dessa atividade, você foi capaz de:

✅ Criar uma função serverless usando AWS Lambda

✅ Armazenar e processar imagens com o Amazon S3

✅ Detectar rostos humanos usando o serviço de Amazon Rekognition

✅ Analisar logs gerados com o CloudWatch

✅ Compreender a base da visão computacional na nuvem com AWS

### 💡 O que aprendemos?

| Habilidade              | Descrição                                              |
|-------------------------|--------------------------------------------------------|
| **Serverless (Lambda)** | Executar código sem precisar provisionar servidores   |
| **Amazon Rekognition**  | Analisar imagens e vídeos com IA pronta               |
| **Amazon S3**           | Armazenar objetos (como imagens) na nuvem             |
| **CloudWatch Logs**     | Visualizar logs de execução da função Lambda          |
| **Boas práticas AWS**   | Gerenciar permissões, paths e segurança               |

### 👩‍🏫 Atividade: EDN-Turma-BRSAO179-IA-AWS

Escola da Nuvem © 2025

Instrutor Técnico AWS :  [Heberton Geovane](https://www.linkedin.com/in/heberton-geovane/)

Objetivo da Atividade

O objetivo deste repositório é compartilhar o conhecimento adquirido durante as aulas, com foco no desenvolvimento de habilidades em:

Fundamentos de Inteligência Artificial
Computação em Nuvem (AWS)
Boas Práticas de Desenvolvimento de Software
Trabalhos práticos com projetos colaborativos

Este repositório faz parte de um projeto **educacional em tecnologia**, promovido pela **Escola da Nuvem** com apoio da **AWS**. Nosso objetivo é formar talentos preparados para os desafios do mercado digital, com base em práticas modernas de desenvolvimento, **metodologias ágeis**, e **computação em nuvem**.

AWS e Escola da Nuvem

![image - 2025-05-16T110140 115](https://github.com/user-attachments/assets/4e3beceb-67fd-4d0b-8667-6a58f1e8cdfe)
