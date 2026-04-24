---
name: azure-functions-to-lambda
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Azure Functions → AWS Lambda Migration

## Overview

**COMPLEXITY: HIGH** - This is the most complex Azure → AWS migration requiring significant architectural changes. Azure Functions and Lambda have fundamentally different programming models.

## Package Migration

### Azure Packages (Remove)
```xml
<PackageReference Include="Microsoft.Azure.Functions.Worker" Version="1.x.x" />
<PackageReference Include="Microsoft.Azure.Functions.Worker.Sdk" Version="1.x.x" />
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Http" Version="3.x.x" />
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Storage" Version="6.x.x" />
<PackageReference Include="Microsoft.Azure.Functions.Worker.Extensions.Timer" Version="4.x.x" />
```

### AWS Packages (Add)
```xml
<PackageReference Include="Amazon.Lambda.Core" Version="2.x.x" />
<PackageReference Include="Amazon.Lambda.Serialization.SystemTextJson" Version="2.x.x" />
<PackageReference Include="Amazon.Lambda.APIGatewayEvents" Version="2.x.x" />
<PackageReference Include="Amazon.Lambda.SQSEvents" Version="2.x.x" />
<PackageReference Include="Amazon.Lambda.S3Events" Version="3.x.x" />
<PackageReference Include="AWSSDK.Extensions.NETCore.Setup" Version="3.7.x" />
```

## Trigger Mapping

| Azure Functions Trigger | AWS Lambda Trigger | Notes |
|------------------------|-------------------|-------|
| HTTP Trigger | API Gateway + Lambda | Requires API Gateway setup |
| Timer Trigger | EventBridge (CloudWatch Events) | Cron expression format different |
| Queue Trigger (Storage) | SQS Trigger | Direct equivalent |
| Queue Trigger (Service Bus) | SQS Trigger + SNS | Requires SQS setup |
| Blob Trigger | S3 Event Notification | Different event structure |
| Cosmos DB Trigger | DynamoDB Streams | If using DynamoDB |

## Project Structure Changes

### Azure Functions (Before)
```
MyFunctionApp/
├── host.json
├── local.settings.json
├── MyFunctionApp.csproj
├── Program.cs (host builder)
├── HttpFunction.cs
├── QueueFunction.cs
└── TimerFunction.cs
```

### AWS Lambda (After)
```
MyLambdaFunctions/
├── src/
│   ├── HttpFunction/
│   │   ├── Function.cs
│   │   ├── aws-lambda-tools-defaults.json
│   │   └── HttpFunction.csproj
│   ├── QueueFunction/
│   │   ├── Function.cs
│   │   ├── aws-lambda-tools-defaults.json
│   │   └── QueueFunction.csproj
│   └── Shared/
│       ├── Services/
│       └── Models/
└── template.yaml (SAM/CloudFormation)
```

**Key difference**: AWS Lambda best practice is **one function per project** (not multiple functions in one project like Azure).

## HTTP Trigger Migration

### Azure Functions (Before)
```csharp
using Microsoft.Azure.Functions.Worker;
using Microsoft.Azure.Functions.Worker.Http;
using Microsoft.Extensions.Logging;

public class HttpFunction
{
    private readonly ILogger<HttpFunction> _logger;
    private readonly IUserService _userService;

    public HttpFunction(ILogger<HttpFunction> logger, IUserService userService)
    {
        _logger = logger;
        _userService = userService;
    }

    [Function("GetUser")]
    public async Task<HttpResponseData> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", Route = "users/{id}")] HttpRequestData req,
        int id)
    {
        _logger.LogInformation("Getting user {UserId}", id);

        var user = await _userService.GetUserAsync(id);
        
        var response = req.CreateResponse(user != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);
        await response.WriteAsJsonAsync(user);
        
        return response;
    }
}
```

### AWS Lambda (After)
```csharp
using Amazon.Lambda.Core;
using Amazon.Lambda.APIGatewayEvents;
using Amazon.Lambda.Serialization.SystemTextJson;
using System.Text.Json;

[assembly: LambdaSerializer(typeof(DefaultLambdaJsonSerializer))]

public class Function
{
    private readonly IUserService _userService;
    private readonly ILogger<Function> _logger;

    public Function()
    {
        // Manual DI setup (or use custom runtime)
        var services = new ServiceCollection();
        ConfigureServices(services);
        var serviceProvider = services.BuildServiceProvider();
        
        _userService = serviceProvider.GetRequiredService<IUserService>();
        _logger = serviceProvider.GetRequiredService<ILogger<Function>>();
    }

    public async Task<APIGatewayProxyResponse> FunctionHandler(
        APIGatewayProxyRequest request, 
        ILambdaContext context)
    {
        _logger.LogInformation("Getting user from path {Path}", request.Path);

        // Parse path parameter
        if (!request.PathParameters.TryGetValue("id", out var idStr) || !int.TryParse(idStr, out var id))
        {
            return new APIGatewayProxyResponse
            {
                StatusCode = 400,
                Body = JsonSerializer.Serialize(new { error = "Invalid user ID" })
            };
        }

        var user = await _userService.GetUserAsync(id);

        if (user == null)
        {
            return new APIGatewayProxyResponse
            {
                StatusCode = 404,
                Body = JsonSerializer.Serialize(new { error = "User not found" })
            };
        }

        return new APIGatewayProxyResponse
        {
            StatusCode = 200,
            Body = JsonSerializer.Serialize(user),
            Headers = new Dictionary<string, string> { { "Content-Type", "application/json" } }
        };
    }

    private static void ConfigureServices(IServiceCollection services)
    {
        // AWS SDK clients
        services.AddDefaultAWSOptions(new AWSOptions());
        services.AddAWSService<IAmazonS3>();
        
        // Application services
        services.AddSingleton<IUserService, UserService>();
        
        // Logging
        services.AddLogging(builder =>
        {
            builder.AddLambdaLogger();
        });
    }
}
```

## Queue Trigger Migration

### Azure Functions (Before)
```csharp
[Function("ProcessOrder")]
public async Task Run(
    [QueueTrigger("orders")] string message,
    FunctionContext context)
{
    var logger = context.GetLogger("ProcessOrder");
    var order = JsonSerializer.Deserialize<Order>(message);
    
    await _orderService.ProcessAsync(order);
    
    logger.LogInformation("Processed order {OrderId}", order.Id);
}
```

### AWS Lambda (After)
```csharp
using Amazon.Lambda.SQSEvents;

public async Task FunctionHandler(SQSEvent sqsEvent, ILambdaContext context)
{
    foreach (var record in sqsEvent.Records)
    {
        context.Logger.LogInformation($"Processing message {record.MessageId}");
        
        var order = JsonSerializer.Deserialize<Order>(record.Body);
        
        await _orderService.ProcessAsync(order);
        
        // Lambda automatically deletes message if no exception thrown
    }
}
```

## Timer Trigger Migration

### Azure Functions (Before)
```csharp
[Function("DailyCleanup")]
public async Task Run(
    [TimerTrigger("0 0 2 * * *")] TimerInfo timer, // 2 AM daily
    FunctionContext context)
{
    var logger = context.GetLogger("DailyCleanup");
    
    await _cleanupService.CleanupOldRecordsAsync();
    
    logger.LogInformation("Cleanup completed");
}
```

### AWS Lambda (After)
```csharp
// No special event type needed for EventBridge
public async Task FunctionHandler(object input, ILambdaContext context)
{
    context.Logger.LogInformation("Starting cleanup");
    
    await _cleanupService.CleanupOldRecordsAsync();
    
    context.Logger.LogInformation("Cleanup completed");
}
```

**EventBridge Rule (Terraform)**:
```hcl
resource "aws_cloudwatch_event_rule" "daily_cleanup" {
  name                = "daily-cleanup"
  schedule_expression = "cron(0 2 * * ? *)" # 2 AM daily UTC
}

resource "aws_cloudwatch_event_target" "lambda" {
  rule      = aws_cloudwatch_event_rule.daily_cleanup.name
  target_id = "DailyCleanupLambda"
  arn       = aws_lambda_function.daily_cleanup.arn
}
```

## Blob/S3 Trigger Migration

### Azure Functions (Before)
```csharp
[Function("ProcessUpload")]
public async Task Run(
    [BlobTrigger("uploads/{name}")] Stream blob,
    string name,
    FunctionContext context)
{
    var logger = context.GetLogger("ProcessUpload");
    
    await _fileProcessor.ProcessAsync(blob, name);
    
    logger.LogInformation("Processed file {FileName}", name);
}
```

### AWS Lambda (After)
```csharp
using Amazon.Lambda.S3Events;

public async Task FunctionHandler(S3Event s3Event, ILambdaContext context)
{
    foreach (var record in s3Event.Records)
    {
        var bucketName = record.S3.Bucket.Name;
        var objectKey = record.S3.Object.Key;
        
        context.Logger.LogInformation($"Processing {objectKey} from {bucketName}");
        
        // Download object
        var getRequest = new GetObjectRequest
        {
            BucketName = bucketName,
            Key = objectKey
        };
        
        using var response = await _s3Client.GetObjectAsync(getRequest);
        using var stream = response.ResponseStream;
        
        await _fileProcessor.ProcessAsync(stream, objectKey);
    }
}
```

## Configuration Migration

### Azure Functions (local.settings.json)
```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "UseDevelopmentStorage=true",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "ConnectionStrings__Database": "Server=..."
  }
}
```

### AWS Lambda (Environment Variables)
```hcl
resource "aws_lambda_function" "my_function" {
  function_name = "MyFunction"
  
  environment {
    variables = {
      DATABASE_CONNECTION_STRING = data.aws_ssm_parameter.db_connection.value
      AWS_REGION                = "us-east-1"
    }
  }
}
```

## Dependency Injection Pattern

**Problem**: Azure Functions has built-in DI via `Program.cs` host builder. Lambda doesn't have this by default.

**Solution 1: Manual DI (simplest)**:
```csharp
public class Function
{
    private readonly IServiceProvider _serviceProvider;

    public Function()
    {
        var services = new ServiceCollection();
        ConfigureServices(services);
        _serviceProvider = services.BuildServiceProvider();
    }
    
    public async Task<APIGatewayProxyResponse> FunctionHandler(...)
    {
        var service = _serviceProvider.GetRequiredService<IMyService>();
        // ...
    }
}
```

**Solution 2: Custom Runtime (advanced)**:
```xml
<PackageReference Include="Amazon.Lambda.RuntimeSupport" Version="1.x.x" />
```

## Infrastructure (Terraform)

```hcl
# HTTP Lambda with API Gateway
resource "aws_lambda_function" "http_function" {
  filename         = "http-function.zip"
  function_name    = "GetUser"
  role            = aws_iam_role.lambda_exec.arn
  handler         = "HttpFunction::HttpFunction.Function::FunctionHandler"
  runtime         = "dotnet8"
  timeout         = 30
  memory_size     = 512

  environment {
    variables = {
      DATABASE_CONNECTION = data.aws_ssm_parameter.db.value
    }
  }
}

# API Gateway
resource "aws_apigatewayv2_api" "main" {
  name          = "my-api"
  protocol_type = "HTTP"
}

resource "aws_apigatewayv2_integration" "lambda" {
  api_id           = aws_apigatewayv2_api.main.id
  integration_type = "AWS_PROXY"
  integration_uri  = aws_lambda_function.http_function.invoke_arn
}

resource "aws_apigatewayv2_route" "get_user" {
  api_id    = aws_apigatewayv2_api.main.id
  route_key = "GET /users/{id}"
  target    = "integrations/${aws_apigatewayv2_integration.lambda.id}"
}

# Queue Lambda
resource "aws_lambda_function" "queue_function" {
  filename      = "queue-function.zip"
  function_name = "ProcessOrder"
  role         = aws_iam_role.lambda_exec.arn
  handler      = "QueueFunction::QueueFunction.Function::FunctionHandler"
  runtime      = "dotnet8"
}

resource "aws_lambda_event_source_mapping" "sqs" {
  event_source_arn = aws_sqs_queue.orders.arn
  function_name    = aws_lambda_function.queue_function.arn
  batch_size       = 10
}
```

## IAM Role

```hcl
resource "aws_iam_role" "lambda_exec" {
  name = "lambda_exec_role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      }
    ]
  })
}

resource "aws_iam_role_policy_attachment" "lambda_basic" {
  role       = aws_iam_role.lambda_exec.name
  policy_arn = "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
}
```

## Migration Checklist

- [ ] Decide architecture: Monolith function vs multiple functions
- [ ] Remove Azure Functions NuGet packages
- [ ] Add AWS Lambda NuGet packages
- [ ] Split functions into separate projects (recommended)
- [ ] Convert HTTP triggers to API Gateway handlers
- [ ] Convert queue triggers to SQS event handlers
- [ ] Convert timer triggers to EventBridge rules
- [ ] Convert blob triggers to S3 event handlers
- [ ] Implement manual DI or custom runtime
- [ ] Migrate configuration to environment variables/Parameter Store
- [ ] Update logging to use ILambdaContext.Logger
- [ ] Create deployment packages (zip files)
- [ ] Set up API Gateway + Lambda integration
- [ ] Configure EventBridge rules for timers
- [ ] Test cold start performance
- [ ] Optimize memory/timeout settings
- [ ] Set up CloudWatch Logs
- [ ] Update CI/CD for Lambda deployment

## Common Pitfalls

⚠️ **Cold starts**: Lambda has cold start latency (especially for .NET) - use provisioned concurrency for critical functions  
⚠️ **Request/response model**: API Gateway has size limits (6 MB) and timeout (30s) - design accordingly  
⚠️ **DI lifetime**: Lambda reuses instances - be careful with singleton vs scoped services  
⚠️ **Deployment**: Each function needs separate deployment package (unlike Azure Functions single deployment)  
⚠️ **Local development**: No equivalent to Azure Functions Core Tools - use AWS SAM CLI instead  
⚠️ **Context**: ILambdaContext provides request ID, function name, memory limit - different from FunctionContext  

## Testing Locally (AWS SAM)

Install AWS SAM CLI, then:

```yaml
# template.yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Resources:
  GetUserFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: HttpFunction::HttpFunction.Function::FunctionHandler
      Runtime: dotnet8
      Events:
        GetUser:
          Type: Api
          Properties:
            Path: /users/{id}
            Method: get
```

```bash
sam build
sam local start-api
```

## Success Criteria

✅ All Azure Functions converted to Lambda  
✅ HTTP triggers working with API Gateway  
✅ Queue triggers working with SQS  
✅ Timer triggers working with EventBridge  
✅ Dependency injection working  
✅ Configuration migrated  
✅ Logging to CloudWatch working  
✅ Cold start performance acceptable  
✅ Deployment automation in place

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
