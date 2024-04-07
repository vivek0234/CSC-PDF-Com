#Python Code to link with the telegram bot, API Gateway, S3 services.
import boto3
import json
import base64
import http.client
import tempfile
import os
import urllib.request

s3_bucket_name = 'file-compress-bot-input'//change to your input s3 bucket name
token='7179842548:AAFZKEwNV95vW2pIwIz3odODVTWv_WJjo0E'//Keep your telegram bot token id
def download_from_s3(bucket_name, file_key):
    s3 = boto3.client('s3')
    local_file_path = '/tmp/' + file_key.split('/')[-1]
    s3.download_file(bucket_name, file_key, local_file_path)
    return local_file_path

def send_message(text,chat_id):
    
    host = 'api.telegram.org'
    conn = http.client.HTTPSConnection(host)
    headers = {"Content-type": "application/json"}
    payload = {
        "chat_id": chat_id,
        "text": text,
    }
    conn.request("POST", f"/bot{token}/sendMessage", body=json.dumps(payload), headers=headers)
    response = conn.getresponse()
    print(response.status, response.reason)
    print(response.read().decode('utf-8'))
    return response.reason

    

def compress_pdf(file_path):
    with open(file_path, 'rb') as f:
        file_data = f.read()
    base64_data = base64.b64encode(file_data).decode('utf-8')
    
    payload = {
        "Parameters": [
            {
                "Name": "File",
                "FileValue": {
                    "Name": os.path.basename(file_path),
                    "Data": base64_data
                }
            },
            {
                "Name": "StoreFile",
                "Value": True
            }
        ]
    }

    conn = http.client.HTTPSConnection("v2.convertapi.com")
    headers = {
        'Content-Type': 'application/json'
    }

    conn.request("POST", "/convert/pdf/to/compress?Secret=qUyj9LLdEKgr9QpF", json.dumps(payload), headers)
    res = conn.getresponse()
    data = res.read()
    compressed_data = json.loads(data.decode("utf-8"))
    print(compressed_data)

    # Check if the necessary keys are present in the response
    if "Files" in compressed_data and compressed_data["Files"]:
        file_info = compressed_data["Files"][0]
        if "Url" in file_info:
            # Download compressed file
            url = file_info["Url"]
            temp_dir = tempfile.mkdtemp()
            compressed_file_path = os.path.join(temp_dir, os.path.basename(file_path))
            urllib.request.urlretrieve(url, compressed_file_path)

            return compressed_file_path
        else:
            raise ValueError("Unable to compress file. URL not found in the response from ConvertAPI.")
    else:
        raise ValueError("Unable to compress file. Files not found in the response from ConvertAPI.")

def upload_output_file_to_s3(bucket_name, local_file_path, s3_file_key):
    s3 = boto3.client('s3')
    s3.upload_file(local_file_path, bucket_name, s3_file_key)


def file_compresser(s3_key):
    
    # bucket_name = event['Records'][0]['s3']['bucket']['name']
    # object_key = event['Records'][0]['s3']['object']['key']
    file_key = s3_key

    local_file_path = download_from_s3(s3_bucket_name, file_key)
    print(local_file_path)
    compressed_file_path = compress_pdf(local_file_path)

    # Assuming you want to upload the compressed file with the same key prefixed with 'compressed/'
    compressed_file_key =file_key.split('/')[-1]
    upload_output_file_to_s3('file-compress-bot-output', compressed_file_path, compressed_file_key)

    return compressed_file_path

def upload_file_to_s3(file_path, s3_key):
    # Upload file to S3 bucket
    s3 = boto3.client('s3')
    with open(file_path, 'rb') as f:
        s3.upload_fileobj(f, s3_bucket_name, s3_key)
    print('Uploaded to S3')
        
def download_telegram_file(file_id):
    # Construct the Telegram Bot API URL
    api_url = f"/bot{token}/getFile?file_id={file_id}"
    
    # Connect to the Telegram Bot API
    connection = http.client.HTTPSConnection("api.telegram.org")
    
    # Make a GET request to the Telegram Bot API to get file path
    connection.request("GET", api_url)
    response = connection.getresponse()
    
    # Check if the request was successful
    if response.status == 200:
        # Read the response data
        response_data = response.read()
        
        # Parse the JSON response
        file_info = json.loads(response_data)
        
        # Extract the file path from the response
        file_path = file_info['result']['file_path']
        
        # Construct the URL to download the file
        download_url = f"https://api.telegram.org/file/bot{token}/{file_path}"
        
        # Download the file
        file_name = file_path.split('/')[-1]  # Extract file name from the file path
        downloaded_file_path = f"/tmp/{file_name}"  # Save file to /tmp directory in Lambda
        connection = http.client.HTTPSConnection("api.telegram.org")
        connection.request("GET", download_url)
        response = connection.getresponse()
        
        # Check if the file was successfully downloaded
        if response.status == 200:
            with open(downloaded_file_path, 'wb') as f:
                f.write(response.read())
                
            return downloaded_file_path
        
        else:
            # Handle download failure
            raise Exception(f"Failed to download file. Status code: {response.status}")
    
    else:
        return None

def send_document(document_path, chat_id):
    # Open the document file
    with open(document_path, 'rb') as file:
        # Read the file content
        document_data = file.read()
    
    # Construct the HTTP request
    boundary = '--------------------------098765432109876543210987654'
    document_name = document_path.split('/')[-1]
    
    # Construct the request body
    body = (
        '--' + boundary + '\r\n' +
        'Content-Disposition: form-data; name="chat_id"\r\n\r\n' +
        str(chat_id) + '\r\n' +
        '--' + boundary + '\r\n' +
        'Content-Disposition: form-data; name="document"; filename="' + document_name + '"\r\n' +
        'Content-Type: application/pdf\r\n\r\n'
    )
    
    # Append document data
    body_bytes = body.encode('utf-8')
    body_bytes += document_data + b'\r\n'
    
    # Add boundary terminator
    body_bytes += b'--' + boundary.encode() + b'--\r\n'
    
    # Construct headers
    headers = {
        'Content-Type': 'multipart/form-data; boundary=' + boundary
    }
    
    # Construct the connection to the Telegram API
    connection = http.client.HTTPSConnection("api.telegram.org")
    connection.request("POST", f"/bot{token}/sendDocument", body_bytes, headers)
    
    # Get the response
    response = connection.getresponse()
    
    # Read and decode the response data
    response_data = response.read().decode("utf-8")
    
    # Check if the request was successful
    if response.status == 200:
        print("Document sent successfully.")
    else:
        print(f"Failed to send document. Status code: {response.status}")
        print(response_data)




 
def lambda_handler(event,context):
    event = json.loads(event['body'])
    print(event)
    chat_id = event['message']['chat']['id']
    
    # Check if the message is the /start command
    if 'text' in event['message'] and event['message']['text'] == '/start':
        text= 'Upload a pdf file to compress' 
        return send_message(text,chat_id)
    
    # Check if the message contains a document
    if 'document' in event['message']:
        # Get file details
        file_id = event['message']['document']['file_id']
        file_name = event['message']['document']['file_name']
        
        # Check if the uploaded file is a PDF
        if not file_name.lower().endswith('.pdf'):
            
            text= 'Please upload only PDF files.'
            return send_message(text,chat_id)
        
        
        # Download the file from Telegram
        # file_path = download_telegram_file(file_id)
        file_path = download_telegram_file(file_id)
        if file_path is None:
            return send_message('Failed to get file info from Telegram Bot API. Please try again with another file.',chat_id)
        print(file_path)
        # send_message(file_path,chat_id)
        # Upload the file to S3
        s3_key = f"{chat_id}---{file_name}"
        upload_file_to_s3(file_path, s3_key)
        compressed_file_path=file_compresser(s3_key)
        print(compressed_file_path)
        # return send_message(compressed_file_path,chat_id)
        return send_document(compressed_file_path,chat_id)
        
    else:
        text='No pdf found.Please try again.'
        return send_message(text,chat_id)
