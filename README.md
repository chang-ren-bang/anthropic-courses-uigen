# UIGen

AI-powered React component generator with live preview, powered by Claude AI through AWS Bedrock.

## Prerequisites

- Node.js 18+
- npm
- AWS Account with Bedrock access (optional - app works without it)

## Setup

### 1. Install Dependencies and Initialize Database

```bash
npm run setup
```

This command will:

- Install all dependencies
- Generate Prisma client
- Run database migrations

### 2. Configure AWS Bedrock (Optional)

The application can run in two modes:

- **AI Mode**: Uses Claude AI via AWS Bedrock for intelligent component generation
- **Mock Mode**: Returns static pre-built components (no AWS credentials needed)

To use AI mode, create a `.env` file with your AWS credentials:

```bash
AWS_REGION=ap-northeast-1
AWS_ACCESS_KEY_ID=your-access-key-id
AWS_SECRET_ACCESS_KEY=your-secret-access-key
```

Use `.env.example` as a template:

```bash
cp .env.example .env
# Edit .env with your actual credentials
```

#### Required AWS IAM Permissions

Your AWS IAM user/role needs the following permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel",
        "bedrock:InvokeModelWithResponseStream"
      ],
      "Resource": [
        "arn:aws:bedrock:ap-northeast-1::foundation-model/anthropic.claude-3-haiku-20240307-v1:0",
        "arn:aws:bedrock:ap-northeast-1:*:inference-profile/apac.anthropic.claude-3-haiku-20240307-v1:0"
      ]
    }
  ]
}
```

**Model Used**: Claude 3 Haiku via APAC inference profile (`apac.anthropic.claude-3-haiku-20240307-v1:0`)

## Running the Application

### Development

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

## Usage

1. Sign up or continue as anonymous user
2. Describe the React component you want to create in the chat
3. View generated components in real-time preview
4. Switch to Code view to see and edit the generated files
5. Continue iterating with the AI to refine your components

## Features

- AI-powered component generation using Claude
- Live preview with hot reload
- Virtual file system (no files written to disk)
- Syntax highlighting and code editor
- Component persistence for registered users
- Export generated code

## Tech Stack

- **Frontend**: Next.js 15 with App Router, React 19, TypeScript
- **Styling**: Tailwind CSS v4
- **Database**: Prisma with SQLite
- **AI Provider**: AWS Bedrock (Claude 3 Haiku via APAC inference profile)
- **AI SDK**: Vercel AI SDK v4.3.19
- **AWS SDK**: @ai-sdk/amazon-bedrock v2.2.12

## Architecture

- **Virtual File System**: All generated files are stored in memory, no files written to disk during development
- **Real-time Preview**: React components are rendered instantly using Babel Standalone
- **Streaming Responses**: Claude AI responses stream in real-time for better UX
- **Tool Calling**: AI can create, edit, rename, and delete files through structured tool calls

## Troubleshooting

### AWS Bedrock Errors

**"Access denied" or "UnauthorizedException"**
- Verify your AWS credentials are correct in `.env`
- Ensure IAM permissions include `bedrock:InvokeModel` and `bedrock:InvokeModelWithResponseStream`
- Check that Bedrock is available in your AWS region (ap-northeast-1)

**"Model not found" or "ValidationException"**
- Confirm the model ID matches: `apac.anthropic.claude-3-haiku-20240307-v1:0`
- Ensure you're using an inference profile (starts with `apac.` or `us.`)
- Some regions may not support all models - check AWS Bedrock model availability

**App works but shows "static response" messages**
- This means AWS credentials are not configured or invalid
- The app falls back to mock mode with pre-built components
- Check your `.env` file has all three variables: `AWS_REGION`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`

### Development Issues

**Port 3000 already in use**
- Next.js will automatically use port 3001 if 3000 is taken
- Or manually specify: `npm run dev -- -p 3001`

**Build errors after updating dependencies**
- Clear node_modules and reinstall: `rm -rf node_modules package-lock.json && npm install --legacy-peer-deps`
- Regenerate Prisma client: `npx prisma generate`

## Cost Considerations

Using AWS Bedrock with Claude 3 Haiku:
- **Input**: ~$0.25 per million tokens
- **Output**: ~$1.25 per million tokens
- Typical component generation uses 2,000-5,000 tokens
- Estimated cost per component: $0.005-$0.01

The APAC inference profile distributes requests across multiple regions for better availability and performance.

## Migration from Anthropic API

This project was migrated from Anthropic's direct API to AWS Bedrock. Key changes:

- **Before**: Used `@ai-sdk/anthropic` with `ANTHROPIC_API_KEY`
- **After**: Uses `@ai-sdk/amazon-bedrock` with AWS credentials
- **Model**: Claude 3 Haiku (previous: aimed for Claude Haiku 4.5, but not available with on-demand throughput)
- **Benefits**: Centralized AWS infrastructure, IAM-based access control, cross-region distribution

All functionality remains the same: streaming, tool calling, and mock provider fallback.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT
