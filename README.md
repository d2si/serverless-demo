# TIAD - Serverless

## Objectifs

> Interagir dans une architecture Serverless

Votre rôle est de modifier la Lambda à votre disposition afin de récupérer le flux de données Twitter entrant.

**Etapes :**
* Comprendre le concept FaaS
* Dialoguer avec des composants Serverless
* Récupérer des Tweets
* Stocker les informations

Pour y parvenir, vous devrez utiliser les services suivants :
* **DynamoDB** : Stockage de data NoSQL
* **CloudWatch** : Logging & debugging
* **S3** : Stockage de fichiers (pour les fichiers medias type photo)

Des snippets de code sur comment intéragir avec ces services vous sont fournis un peu plus bas.

## Informations necessaires
Un numéro vous a été fourni, il faudra remplacer les occurrences de `<id>` par ce dernier.

Informations de connexion:
* url de la console AWS: https://icelab14.signin.aws.amazon.com/console
* user: serverless-hands-on-`<id>`
* password: ServerLessHandsOn`<id>`


Ressources mises à disposition:
* table DynamoDB: serverless-hands-on-`<id>`
* bucket S3: serverless-hands-on-`<id>`
* fonction lambda: serverless-hands-on-`<id>`

## Fonctionnement de AWS Lambda
Une lambda est une unité de code qui réagit à des évènements donnés. Une lambda dispose toujours d'un point d'entrée appelé `entrypoint`, représenté sous la forme d'une fonction.
Dans la lambda qui vous sera fournie, l'entrypoint se nomme `lambda_handler`. 
Le premier paramètre passé est un `event`, contenant des metadata sur la source, ainsi que des données.
Ces dernières sont encapsulées dans l'attribut `Records`, disponibles via `event['Records']`, et contiennent les tweets récupérés.
**Attention**: A noter que chaque donnée est `base64` encodée.

Vous devrez donc itérer sur les différents Records, les décoder, afin d'en extraire les informations suivantes, puis les stocker en base de données (DynamoDB) :
* ID du Tweet
* Texte du Tweet
* Date de création
* URL d'un média photo
* ID du média photo associé
* ID de l'auteur du tweet
* Nom d'utilisateur de l'auteur du tweet

Pour récupérer ces données, inspirez vous de la documentation suivante : https://dev.twitter.com/overview/api/tweets

## Modification de la Lambda

Cette partie vous sera exposée sous forme de démo !

## HOWTOs / Snippets
Vous trouverez ci-dessous une série de snippets vous permettant d'intéragir avec les composants exposés précedemment.

### Ecrire des logs depuis la Lambda
Pour écrire des logs depuis Lambda, il suffit simplement d'utiliser la méthode Python native `print` :

```python
print 'Hello world!'
```

### Ecrire dans DynamoDB
Pour écrire dans DynamoDB, il vous faudra utiliser la méthode `put_item` du SDK Boto3. Vous ne nécessitez pas de credentials pour effectuer les appels.
```python
import boto3
from botocore.exceptions import ClientError

def put_data_to_dynamo(data)
    dynamodb = boto3.client('dynamodb')

    try:
        response = dynamodb.put_item(TableName='table_name', Item={
            'Id': {'N': 'MY_ID'}
        })
    except ClientError as e:
        # Do something

        raise e

    print 'Some debug'
```

### Ecrire dans S3
Pour écrire dans DynamoDB, il vous faudra utiliser la méthode `put_object` du SDK Boto3. Vous ne nécessitez pas de credentials pour effectuer les appels.

```python
import boto3
from botocore.exceptions import ClientError

def upload_local_file_to_s3(filename, bucket)
    s3 = boto3.resource('s3')
    data = open(filename, 'rb')
    key = 'uploads/' + filename

    try:
        s3.Bucket(bucket).put_object(Key=key, Body=data)
    except ClientError as e:
        # Do something

        raise e

    print 'Uploading {} to s3://{}/{}'.format(filename, bucket, key)
```

### Récupérer le flux entrant

```python
import base64
import json

def lambda_handler(event, context):
    for record in event['Records']:
        kinesis = record["kinesis"]
        data = json.loads(base64.b64decode(kinesis["data"]).decode('utf-8'))
        print 'Decoded payload: {}'.format(data)

    return 'Processed {} records'.format(len(event['Records']))
```
