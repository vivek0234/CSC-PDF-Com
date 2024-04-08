Title:
PDF Compressor Bot

LinkedIn Article Link:https://www.linkedin.com/pulse/pdf-compressor-telegram-bot-vivek-kayala-ey9ic/
Youtube Link:https://youtu.be/K1kdRvXjsjQ?si=aWE0t76-Ik5f0cj6

Services Used:
1.	AWS Lambda - With the serverless computing service AWS Lambda, you can run code without having to setup or manage servers. 

2.	S3(Simple Storage Service) - Object storage designed to store and access any quantity of information from any location.

3.	Amazon API Gateway – It  simplifies API creation, integration, and management, offering robust security, monitoring, and scalability features, empowering developers to build and deploy APIs effortlessly within AWS infrastructure.

4.	Telegram Bot - Telegram's Bot API to receive messages, process requests, and send responses, enabling a wide range of functionalities from simple commands to complex workflows.

Architecture:
![image](https://github.com/vivek0234/CSC-PDF-Compressor/assets/110586406/3f204611-cb1b-45ec-902d-ae9126d93d77)

Connection through the services:

telegram -> api gateway ->
lambda - > s3 (input )
s3 -> lambda -> convert.api
lambda -> s3 (output)
lambda -> telegram

Steps:
Step-1: Open the AWS Management console in the web browser
  
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/8968fc98-a614-4b16-83d9-30a6d8d29762)


Steps-2: Goto search bar and search for the s3 and create a s3 bucket with name “file-compress-bot-input” and on the public access with ACL enabled
 
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/e4caa054-b703-4dac-bf21-c61305b37883)


Step-3: Create a s3 bucket with name “file-compress-bot-output” and on the public access with ACL enabled
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/d8e331cf-2819-47a9-82d2-afacb6f382bd)

 
Step-4: Create a lambda function with the name pdfcom with the python code to connect with the telegram bot
 
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/ca34ac59-7d10-4db1-b1ff-2858158c11e3)



Step-5: Create a API Gateway with the name bot and deploy the api
 ![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/23d3ae67-5fa4-4926-beb8-7f8b471f9a17)


Step-6: Add the resources to it as POST 
 
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/b9480728-de3f-4667-91ef-5593f2f64bfb)


Step-7: Add API gateway trigger to the lambda function
 
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/a770975a-67c6-4c91-9a96-2affce359439)



Step-8: Create a telegram bot with name pdfcompressorbot
![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/aa05d82b-ffa5-4f1d-9dba-218bff69024b)
 

Step-9: In lambda function write the python code and change the telegram details as created bot token “7179842548:AAFZKEwNV95vW2pIwIz3odODVTWv_WJjo0E”
 ![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/9c9485bd-6b2d-4a9e-a653-fdc8ee39e9d5)


Step-10: Add the s3 bucket name and api names to the code to connect all services
 ![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/36e4ef7d-f81d-49c6-8076-7eabda138b47)

 Output:

 ![image](https://github.com/vivek0234/CSC-PDF-Com/assets/110586406/4d3672a7-639a-4687-8ceb-a35feac4789e)

 Conclusion:

In conclusion, the integration of a PDF compressor via Telegram bot using AWS services offers a versatile and efficient solution for users seeking to compress their documents conveniently. Leveraging AWS services such as Lambda functions, S3 storage, and API Gateway, the Telegram bot provides seamless compression capabilities directly within the messaging platform. This approach not only streamlines the compression process but also ensures data security and scalability, thanks to AWS's robust infrastructure. With the ability to handle PDF compression tasks effortlessly and securely, this solution enhances user experience and productivity while leveraging the power and reliability of AWS cloud services.



