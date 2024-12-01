# BlogStorm Publish

BlogStorm Publish manages content transformation workflows, coordinating with the [BlogStorm LLM](https://github.com/rickhallett/blogstorm-llm) to transform rough ideas into polished blog posts. It handles content scheduling, maintains processing queues, and ensures reliable content delivery to the Ghost blog publishing platform.

## System Overview

The Orchestrator acts as the conductor of the content pipeline, managing the flow of content through various transformation stages. When you submit content, the Orchestrator creates a processing job, schedules it appropriately, and guides it through each enhancement stage using the LLM Pipeline Service. Think of it as an intelligent task manager that ensures your content moves smoothly from ideation to publication.

## Core Features

### Queue Management
The system implements a First-In-First-Out (FIFO) queue for processing content. This ensures fair processing order while preventing system overload.

### Scheduling System
Content can be scheduled for:
- Immediate processing and publication
- Future publication at a specific date and time
- Regular publication on a set cadence (e.g., every Monday at 9 AM)

### State Management
The orchestrator maintains detailed state information for each content piece:
- Current stage in the pipeline
- Processing history and results from each stage
- Scheduled publication time
- Publication status and history

## Getting Started

### Prerequisites
- Node.js (v18 or higher)
- PostgreSQL database
- Redis (for queue management)
- Access to LLM Pipeline Service

### Installation
1. Clone the repository
2. Install dependencies: `npm install`
3. Copy `.env.example` to `.env` and configure environment variables
4. Run database migrations: `npx prisma migrate dev`
5. Start the server: `npm run dev`

## API Documentation

### POST /api/content/submit
Submit new content for processing and optional scheduling.

Request Body:
```json
{
  "content": {
    "initialIdeas": "string",
    "type": "blog_post",
    "preferences": {
      "tone": "casual | professional",
      "targetLength": "short | medium | long"
    }
  },
  "scheduling": {
    "type": "immediate | scheduled | automatic",
    "publishAt": "ISO-8601 datetime",
  }
}
```

### GET /api/content/status/:id
Get the current status of content in the pipeline.

Response:
```json
{
  "status": "queued | processing | ready | published | failed",
  "currentStage": "string",
  "progress": {
    "completedStages": ["string"],
    "nextStage": "string",
  },
  "scheduling": {
    "scheduledTime": "ISO-8601 datetime",
  }
}
```

### GET /api/content/queue
View the current processing queue state.

Response:
```json
{
  "activeJobs": "number",
  "queueLength": "number",
}
```

### PATCH /api/content/:id/schedule
Update the publishing schedule for content.

Request Body:
```json
{
  "newSchedule": {
    "publishAt": "ISO-8601 datetime",
  }
}
```

## Queue System Architecture

The queue system uses Redis for reliable job processing:
- Automatic retry mechanism for failed jobs
- Dead letter queue for failed jobs requiring manual intervention
- Job progress tracking and reporting
- Queue monitoring and health checks

## Database Schema

The service uses PostgreSQL to store:
- Publishing schedules and recurrence patterns
- System configuration and preferences

## Monitoring and Observability

The service provides detailed insights into pipeline operations:
- Queue metrics (length, processing times, success rates)
- Processing stage durations
- Error rates and types
- Publishing success metrics
- System health indicators§

## Development

### Testing
- Unit tests: `npm test`
- Integration tests: `npm run test:integration`
- Load tests: `npm run test:load`

### Environment Variables
```
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/blogstorm-publish
REDIS_URL=redis://localhost:6379
LLM_SERVICE_URL=http://localhost:3001
NODE_ENV=development
```

## Architecture Design Principles

The Orchestrator follows several key design principles:
- Reliability through persistent job storage and queue management
- Scalability through modular processing stages
- Observability through comprehensive logging and monitoring
- Flexibility through configurable processing workflows
- Resilience through robust error handling and recovery

## Future Enhancements

Planned features include:
- Advanced scheduling algorithms using engagement data
- Multi-platform publishing support
- A/B testing of content variations
- Advanced analytics and reporting
- Custom workflow templates

## Contributing

Guidelines for contributing to the project:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request with detailed description
4. Ensure tests pass and coverage maintains standards

## License

MIT License - see LICENSE.md for details