- Azure AI Vision - FACE - Detectar e analisar rostos

A capacidade de detectar e analisar rostos humanos é uma capacidade da IA. Este projeto, será desenvolvido no serviço Face para trabalhar com rostos.

Será criado:

1. Recurso do Azure AI Vision
2. Aplicativo de reconhecimento de rostos com o Azure AI Vision Face SDK

- Como usar: Passo a passo

1. Acesse o Portal do Azure Estúdio
   [https://azure.microsoft.com]

2. Provisionando recurso no Azure AI Vision
   Selecione "+Criar Recurso"
   Na barra de pesquisa, busque por "Face" e crie um recurso
   Preencha: Assinatura, Grupo de Recursos, Região, Nome e Nível de preço
   Clique em "Criar e revisar" e após verificação, em "Criar"
   Após a criação, acesse o recurso e na aba lateral "Gerenciamento de Recursos" e copie  

3. Desenvolvendo um aplicativo de análise facial com o Face SDK
   Crie um novo Cloud Shell no portal do Azure, selecionando o ambiente do PowerShell sem armazenamento na sua assinatura. 
   Na barra de ferramentas do Cloud Shell, em configurações, selecione "ir para a versão clássica
   Clone o repositório do GitHub que contém os arquivos de código
   "rm -r mslearn-ai-vision -f
   git clone https://github.com/MicrosoftLearning/mslearn-ai-visionn"
   Visualize a pasta com os arquivos clonados
   "cd mslearn-ai-vision/Labfiles/face/python/face-api
   ls -a -l"
   Instale o pacote Azure AI Vision SDK e outros pacotes necessários
   "python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-vision-face==1.0.0b2"
   Edite o arquivo de configuração
   "Code .env"
   O arquivo é aberto em um editor de código
   Copie o endpoint e a chave nos lugares solicitados
   Use CTRL+S para salvar e CTRL+Q para sair
   
4. Adicionando código para criar um cliente Face API
   Na linha de comando do Cloud Shell abra o arquivo de código do cliente
   "code analyze-faces.py"
   Encontre o comentário Import namespaces e adicione o seguinte código para importar os namespaces que você precisará para usar o Azure AI Vision SDK:
   "from azure.ai.vision.face import FaceClient
    from azure.ai.vision.face.models import FaceDetectionModel, FaceRecognitionModel, FaceAttributeTypeDetection01
    from azure.core.credentials import AzureKeyCredential"
   Localize o comentário "Authenticate Face Client" e adicione o seguinte código:
    "face_client = FaceClient(
          endpoint=cog_endpoint,
          credential=AzureKeyCredential(cog_key))"
   
5. Adicionando código para detectar e analisar rostos
   Encontre Especificar características faciais a serem recuperadas"e digite o codigo:
   "features = [FaceAttributeTypeDetection01.HEAD_POSE,
                 FaceAttributeTypeDetection01.OCCLUSION,
                 FaceAttributeTypeDetection01.ACCESSORIES]"
   Encontre "Obter faces e digite o código: 
   "with open(image_file, mode="rb") as image_data:
         detected_faces = face_client.detect(
            image_content=image_data.read(),
            detection_model=FaceDetectionModel.DETECTION01,
            recognition_model=FaceRecognitionModel.RECOGNITION01,
            return_face_id=False,
            return_face_attributes=features,
     )

  face_count = 0
  if len(detected_faces) > 0:
       print(len(detected_faces), 'faces detected.')
       for face in detected_faces:
    
           # Get face properties
           face_count += 1
           print('\nFace number {}'.format(face_count))
           print(' - Head Pose (Yaw): {}'.format(face.face_attributes.head_pose.yaw))
           print(' - Head Pose (Pitch): {}'.format(face.face_attributes.head_pose.pitch))
           print(' - Head Pose (Roll): {}'.format(face.face_attributes.head_pose.roll))
           print(' - Forehead occluded?: {}'.format(face.face_attributes.occlusion["foreheadOccluded"]))
           print(' - Eye occluded?: {}'.format(face.face_attributes.occlusion["eyeOccluded"]))
           print(' - Mouth occluded?: {}'.format(face.face_attributes.occlusion["mouthOccluded"]))
           print(' - Accessories:')
           for accessory in face.face_attributes.accessories:
               print('   - {}'.format(accessory.type))
           # Annotate faces in the image
           annotate_faces(image_file, detected_faces)"
   Salve
   Execute o código através do comando:
   "python analyze-faces.py images/face1.jpg
   Execute novamente o programa
   Faca download da nova imagem gerada:
   "download detected_faces.jpg"
   
6. Código Fonte:

from dotenv import load_dotenv
import os
import sys
from PIL import Image, ImageDraw
from matplotlib import pyplot as plt

# Import namespaces
from azure.ai.vision.face import FaceClient
from azure.ai.vision.face.models import FaceDetectionModel, FaceRecognitionModel, FaceAttributeTypeDetection01
from azure.core.credentials import AzureKeyCredential

def main():

    # Clear the console
    os.system('cls' if os.name=='nt' else 'clear')

    try:
        # Get Configuration Settings
        load_dotenv()
        cog_endpoint = os.getenv('AI_SERVICE_ENDPOINT')
        cog_key = os.getenv('AI_SERVICE_KEY')

        # Get image
        image_file = 'images/face1.jpg'
        if len(sys.argv) > 1:
            image_file = sys.argv[1]


        # Authenticate Face client
        face_client = FaceClient(
             endpoint=cog_endpoint,
             credential=AzureKeyCredential(cog_key))


        # Specify facial features to be retrieved
        features = [FaceAttributeTypeDetection01.HEAD_POSE,
                     FaceAttributeTypeDetection01.OCCLUSION,
                     FaceAttributeTypeDetection01.ACCESSORIES]

        # Get faces
        with open(image_file, mode="rb") as image_data:
             detected_faces = face_client.detect(
                 image_content=image_data.read(),
                 detection_model=FaceDetectionModel.DETECTION01,
                 recognition_model=FaceRecognitionModel.RECOGNITION01,
                 return_face_id=False,
                 return_face_attributes=features,
            )

        face_count = 0
        if len(detected_faces) > 0:
             print(len(detected_faces), 'faces detected.')
             for face in detected_faces:
    
                 # Get face properties
                 face_count += 1
                 print('\nFace number {}'.format(face_count))
                 print(' - Head Pose (Yaw): {}'.format(face.face_attributes.head_pose.yaw))
                 print(' - Head Pose (Pitch): {}'.format(face.face_attributes.head_pose.pitch))
                 print(' - Head Pose (Roll): {}'.format(face.face_attributes.head_pose.roll))
                 print(' - Forehead occluded?: {}'.format(face.face_attributes.occlusion["foreheadOccluded"]))
                 print(' - Eye occluded?: {}'.format(face.face_attributes.occlusion["eyeOccluded"]))
                 print(' - Mouth occluded?: {}'.format(face.face_attributes.occlusion["mouthOccluded"]))
                 print(' - Accessories:')
                 for accessory in face.face_attributes.accessories:
                     print('   - {}'.format(accessory.type))
                 # Annotate faces in the image
                 annotate_faces(image_file, detected_faces)
 

    except Exception as ex:
        print(ex)

def annotate_faces(image_file, detected_faces):
    print('\nAnnotating faces in image...')

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    color = 'lightgreen'

    # Annotate each face in the image
    face_count = 0
    for face in detected_faces:
        face_count += 1
        r = face.face_rectangle
        bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
        draw = ImageDraw.Draw(image)
        draw.rectangle(bounding_box, outline=color, width=5)
        annotation = 'Face number {}'.format(face_count)
        plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)
    
    # Save annotated image
    plt.imshow(image)
    outputfile = 'detected_faces.jpg'
    fig.savefig(outputfile)
    print(f'  Results saved in {outputfile}\n')


if __name__ == "__main__":
    main()
   
- Tecnologias Usadas

 1. Microsoft Azure - (https://azure.microsoft.com/)
 2. Azure Face - (https://azure.microsoft.com/)
 3. ARM Templates - (https://learn.microsoft.com/azure/azure-resource-manager/templates/overview)
 4. Markdown para documentação

- Autor
  Dannuza Martins Cardoso Silva
 
 


