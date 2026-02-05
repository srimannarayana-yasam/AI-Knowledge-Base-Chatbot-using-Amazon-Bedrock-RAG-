# AWS Serverless RAG Application

A production-ready Retrieval-Augmented Generation (RAG) application built entirely with AWS serverless services. This project demonstrates how to build an intelligent Q&A system that combines the power of vector search with state-of-the-art AI models.

![Architecture Diagram](./architecture-diagram.svg)

## 🚀 Features

- **Serverless Architecture** - Zero server management, auto-scaling, pay-per-use
- **Intelligent Retrieval** - Semantic search using vector embeddings
- **AI-Powered Responses** - Context-aware answers using AWS Bedrock Foundation Models
- **Modern UI** - Clean, responsive interface hosted on S3
- **CORS Enabled** - Secure cross-origin communication
- **Cost Efficient** - Only pay for what you use
- **Production Ready** - Built with AWS best practices

## 🏗️ Architecture

The application follows a modern serverless RAG pattern:

```
User Browser
    ↓
S3 Static Website (UI)
    ↓
Lambda Function URL (API + CORS)
    ↓
Bedrock Knowledge Base (Vector Search)
    ↓
Foundation Model (Claude/Titan)
    ↓
Response → Lambda → User
```

### Components

1. **S3 Static Website**
   - Hosts the frontend application (React/Vue/HTML)
   - Configured for static website hosting
   - CloudFront-ready for global distribution

2. **Lambda Function**
   - Function URL for public HTTP access
   - CORS configuration for secure API calls
   - Handles request validation and routing
   - Integrates with Bedrock services

3. **Bedrock Knowledge Base**
   - Vector database for semantic search
   - Embedding generation for queries
   - Retrieves relevant context from your data
   - Supports multiple data sources (S3, Confluence, etc.)

4. **Bedrock Foundation Model**
   - Claude or Amazon Titan models
   - Generates contextual responses
   - Combines retrieved knowledge with AI reasoning

## 📋 Prerequisites

- AWS Account with appropriate permissions
- AWS CLI configured
- Node.js 18.x or later (for frontend)
- Python 3.9+ (for Lambda)
- Basic knowledge of AWS services

## 🛠️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/aws-serverless-rag.git
cd aws-serverless-rag
```

### 2. Create Bedrock Knowledge Base

```bash
# Navigate to AWS Bedrock Console
# Create a new Knowledge Base
# Configure your data source (S3 bucket with documents)
# Note down the Knowledge Base ID
```

### 3. Deploy Lambda Function

```bash
cd lambda
pip install -r requirements.txt -t .
zip -r function.zip .
aws lambda create-function \
  --function-name rag-api-handler \
  --runtime python3.9 \
  --role arn:aws:iam::YOUR_ACCOUNT:role/lambda-bedrock-role \
  --handler index.handler \
  --zip-file fileb://function.zip
```

### 4. Configure Function URL

```bash
aws lambda create-function-url-config \
  --function-name rag-api-handler \
  --auth-type NONE \
  --cors '{
    "AllowOrigins": ["*"],
    "AllowMethods": ["POST", "GET"],
    "AllowHeaders": ["Content-Type"],
    "MaxAge": 86400
  }'
```

### 5. Update Environment Variables

```bash
aws lambda update-function-configuration \
  --function-name rag-api-handler \
  --environment Variables={
    KNOWLEDGE_BASE_ID=your-kb-id,
    MODEL_ID=anthropic.claude-v2
  }
```

### 6. Deploy Frontend

```bash
cd frontend
npm install
npm run build

# Upload to S3
aws s3 sync dist/ s3://your-bucket-name --delete

# Enable static website hosting
aws s3 website s3://your-bucket-name \
  --index-document index.html \
  --error-document error.html
```

### 7. Update API Endpoint

Update the API endpoint in your frontend configuration:

```javascript
// frontend/src/config.js
export const API_ENDPOINT = 'https://your-function-url.lambda-url.region.on.aws';
```

## 📁 Project Structure

```
.
├── README.md
├── architecture-diagram.svg
├── lambda/
│   ├── index.py              # Lambda function handler
│   ├── requirements.txt      # Python dependencies
│   └── utils/
│       ├── bedrock.py        # Bedrock integration
│       └── validators.py     # Input validation
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/       # UI components
│   │   ├── services/         # API services
│   │   ├── App.jsx           # Main app component
│   │   └── config.js         # Configuration
│   ├── package.json
│   └── vite.config.js
├── infrastructure/
│   ├── cloudformation/       # IaC templates
│   └── terraform/            # Terraform configs
└── docs/
    ├── API.md                # API documentation
    └── DEPLOYMENT.md         # Deployment guide
```

## 🔧 Configuration

### Lambda Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `KNOWLEDGE_BASE_ID` | Bedrock Knowledge Base ID | `ABC123XYZ` |
| `MODEL_ID` | Foundation model identifier | `anthropic.claude-v2` |
| `MAX_TOKENS` | Maximum response tokens | `2000` |
| `TEMPERATURE` | Model temperature (0-1) | `0.7` |

### IAM Permissions

Your Lambda execution role needs:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:Retrieve",
        "bedrock:InvokeModel"
      ],
      "Resource": "*"
    }
  ]
}
```

## 🔌 API Reference

### POST /query

Send a question to the RAG system.

**Request:**
```json
{
  "query": "What is the return policy?",
  "max_results": 5
}
```

**Response:**
```json
{
  "answer": "Based on our policy, you can return items within 30 days...",
  "sources": [
    {
      "content": "Return policy excerpt...",
      "score": 0.92,
      "metadata": {
        "source": "policies.pdf",
        "page": 3
      }
    }
  ],
  "timestamp": "2024-02-05T10:30:00Z"
}
```

## 💰 Cost Estimation

Approximate costs for 10,000 requests/month:

| Service | Estimated Cost |
|---------|---------------|
| Lambda (1GB, 3s avg) | $0.60 |
| Bedrock Knowledge Base | $5.00 |
| Bedrock Model Inference | $15.00 |
| S3 Static Hosting | $0.50 |
| **Total** | **~$21/month** |

*Note: Costs vary based on usage patterns and model selection*

## 🚦 Usage Example

```javascript
// Query the API from your frontend
const response = await fetch('https://your-function-url.lambda-url.region.on.aws', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    query: 'How do I reset my password?'
  })
});

const data = await response.json();
console.log(data.answer);
```

## 🧪 Testing

```bash
# Test Lambda function locally
cd lambda
python -m pytest tests/

# Test frontend
cd frontend
npm run test

# Integration test
curl -X POST https://your-function-url.lambda-url.region.on.aws \
  -H "Content-Type: application/json" \
  -d '{"query": "test question"}'
```

## 📊 Monitoring

- **CloudWatch Logs** - Lambda execution logs
- **CloudWatch Metrics** - Function invocations, errors, duration
- **X-Ray** - Distributed tracing (optional)
- **Bedrock Metrics** - Model invocation costs and latency

## 🔒 Security Best Practices

- ✅ Lambda function URL with CORS properly configured
- ✅ Input validation and sanitization
- ✅ Rate limiting (consider API Gateway for production)
- ✅ IAM least-privilege permissions
- ✅ Secrets in AWS Secrets Manager (if needed)
- ✅ CloudFront with WAF for DDoS protection (recommended)

## 🎯 Roadmap

- [ ] Add authentication (Cognito)
- [ ] Implement caching layer (ElastiCache)
- [ ] Add conversation history
- [ ] Multi-language support
- [ ] Streaming responses
- [ ] Advanced analytics dashboard
- [ ] A/B testing for different models

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- AWS Bedrock team for amazing AI capabilities
- Claude AI for powerful language understanding
- The serverless community for inspiration

## 📧 Contact

Your Name - [@yourtwitter](https://twitter.com/yourtwitter)

Project Link: [https://github.com/yourusername/aws-serverless-rag](https://github.com/yourusername/aws-serverless-rag)

## 🌟 Star History

If you find this project useful, please consider giving it a star! ⭐

---

**Built with ❤️ using AWS Serverless Services**
