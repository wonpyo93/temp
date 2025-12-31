## MVP Scope (100 Users, 100 Movies)

### Assumptions:
- 100 active users per month
- 100 movies (avg 2GB each = 200GB total storage)
- 50% users watch previews, 20% make purchases
- Average 10 API calls per user per session
- 2 sessions per user per month

## AWS Infrastructure Costs (Monthly)

### 1. Storage (AWS S3)
```
Movies: 200GB × $0.023/GB = $4.60/month
Thumbnails: 100 × 1MB × $0.023/GB = $0.002/month
Total S3 Storage: ~$5/month
```

### 2. Content Delivery (CloudFront CDN)
```
Data Transfer:
- Preview traffic: 100 users × 50% × 200MB = 10GB
- Full movie traffic: 100 users × 20% × 2GB = 40GB
- Total: 50GB × $0.085/GB = $4.25/month
```

### 3. Database (DynamoDB)
```
On-demand pricing:
- Read requests: 2,000/month × $0.25/million = $0.0005
- Write requests: 500/month × $1.25/million = $0.0006
- Storage: 1GB × $0.25/GB = $0.25
Total DynamoDB: ~$0.25/month
```

### 4. API Gateway
```
API calls: 100 users × 10 calls × 2 sessions = 2,000 calls
2,000 calls × $3.50/million = $0.007/month
```

### 5. Lambda Functions
```
Execution time: 2,000 requests × 500ms avg = 1,000 seconds
1,000 seconds × $0.0000166667/GB-second = $0.017/month
```

### 6. ElastiCache (Optional for MVP)
```
t3.micro: $0.017/hour × 24 × 30 = $12.24/month
(Can be skipped for MVP to save costs)
```

### 7. Other AWS Services
```
CloudWatch Logs: $1/month
Route 53 (DNS): $0.50/month
```

**Total AWS Monthly Cost: ~$23-35/month**

## Development Tools & Services

### 1. Development Environment
```
- AWS Free Tier: $0 (first year)
- Domain name: $12/year
- SSL Certificate: $0 (AWS Certificate Manager)
- Development tools: $0 (VS Code, Git)
```

### 2. Third-party Services
```
- PayPal Developer: $0 (transaction fees only)
- Naver Pay: $0 setup (transaction fees apply)
- Kakao Pay: $0 setup (transaction fees apply)
- OAuth providers: $0 (Google, Facebook, Apple)
```

### 3. Mobile App Distribution
```
- Apple App Store: $99/year
- Google Play Store: $25 one-time
```

### 4. Testing & Monitoring
```
- Firebase Analytics: $0 (free tier)
- Crashlytics: $0 (free tier)
```

## Total Infrastructure Cost Summary

### Initial Setup Costs:
```
- Domain registration: $12
- App Store fees: $124 ($99 + $25)
- Total one-time: $136
```

### Monthly Operating Costs:
```
- AWS infrastructure: $25-35
- Total monthly: $25-35
```

### Annual Cost:
```
- One-time setup: $136
- Monthly × 12: $300-420
- Total first year: $436-556
```

## Development Timeline (1 Full-time Developer)

### Phase 1: Backend Development (6-8 weeks)
```
Week 1-2: AWS Infrastructure Setup
- Set up AWS services (Lambda, DynamoDB, S3, API Gateway)
- Configure OAuth2 providers
- Basic API endpoints

Week 3-4: Core Services
- User authentication service
- Movie management service
- Payment integration (PayPal, Naver, Kakao)

Week 5-6: Business Logic
- 10% preview system
- Progress tracking
- Purchase validation

Week 7-8: Comments & Support Features
- 90% unlock logic
- Comment CRUD operations
- Support button functionality
```

### Phase 2: Mobile App Development (8-10 weeks)
```
Week 9-10: Project Setup & UI Framework
- React Native/Flutter setup
- Basic navigation
- UI component library

Week 11-12: Authentication
- OAuth2 integration
- Token management
- User profile screens

Week 13-14: Video Player
- Custom video player
- Progress tracking
- 10% preview limitation

Week 15-16: Payment Integration
- Payment provider SDKs
- Purchase flow
- Payment confirmation

Week 17-18: Social Features
- Comments system
- Support functionality
- User interactions
```

### Phase 3: Testing & Deployment (3-4 weeks)
```
Week 19-20: Testing
- Unit testing
- Integration testing
- User acceptance testing

Week 21-22: Deployment
- App store submission
- Production deployment
- Monitoring setup
```

### Phase 4: Polish & Launch (2 weeks)
```
Week 23-24: Final Polish
- Bug fixes
- Performance optimization
- Launch preparation
```

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              MOBILE APP                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐             │
│  │   Auth Module   │  │ HLS/DASH Player │  │  Payment Module │             │
│  │   - OAuth2      │  │ - Adaptive Rate │  │  - PayPal       │             │
│  │   - Token Mgmt  │  │ - Segment Based │  │  - Naver Pay    │             │
│  │                 │  │ - 10% Limit     │  │  - Kakao Pay    │             │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘             │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ HLS/DASH Manifest + Segments
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           VIDEO STREAMING LAYER                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐ │
│  │                        AWS CloudFront CDN                               │ │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐         │ │
│  │  │ HLS Manifests   │  │  Video Segments │  │   Thumbnails    │         │ │
│  │  │ (.m3u8 files)   │  │  (.ts files)    │  │   (.jpg files)  │         │ │
│  │  │ - Master        │  │  - 480p, 720p   │  │   - Previews    │         │ │
│  │  │ - Variant       │  │  - 1080p, 4K    │  │   - Posters     │         │ │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘         │ │
│  └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         VIDEO PROCESSING PIPELINE                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐ │
│  │                    AWS Elemental MediaConvert                           │ │
│  │  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐         │ │
│  │  │  Input Video    │  │   Transcoding   │  │  Output Formats │         │ │
│  │  │  - Original MP4 │  │   - Multiple    │  │  - HLS (.m3u8)  │         │ │
│  │  │  - High Quality │  │     Bitrates    │  │  - DASH (.mpd)  │         │ │
│  │  │  - S3 Upload    │  │   - Resolutions │  │  - Segments     │         │ │
│  │  └─────────────────┘  └─────────────────┘  └─────────────────┘         │ │
│  └─────────────────────────────────────────────────────────────────────────┘ │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────────┐ │
│  │                      Processing Workflow                                │ │
│  │  Original Video → MediaConvert → HLS/DASH → S3 → CloudFront             │ │
│  └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              BACKEND API                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐             │
│  │ Streaming Auth  │  │  Manifest Gen   │  │ Progress Track  │             │
│  │ - Token Verify  │  │ - 10% Segments  │  │ - Segment Count │             │
│  │ - Access Control│  │ - Full Access   │  │ - Time Tracking │             │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘             │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              STORAGE LAYER                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐             │
│  │   S3 Bucket     │  │   DynamoDB      │  │   ElastiCache   │             │
│  │                 │  │                 │  │                 │             │
│  │ ┌─────────────┐ │  │ ┌─────────────┐ │  │ ┌─────────────┐ │             │
│  │ │HLS Manifests│ │  │ │Movie Metadata│ │  │ │Manifest Cache│ │             │
│  │ │- master.m3u8│ │  │ │- duration   │ │  │ │- Temp URLs  │ │             │
│  │ │- variant    │ │  │ │- segments   │ │  │ │- Auth tokens│ │             │
│  │ └─────────────┘ │  │ │- bitrates   │ │  │ └─────────────┘ │             │
│  │                 │  │ └─────────────┘ │  └─────────────────┘             │
│  │ ┌─────────────┐ │  │                 │                                  │
│  │ │Video Segments│ │  │ ┌─────────────┐ │                                  │
│  │ │- 480p/*.ts  │ │  │ │Watch Progress│ │                                  │
│  │ │- 720p/*.ts  │ │  │ │- segment_idx│ │                                  │
│  │ │- 1080p/*.ts │ │  │ │- percentage │ │                                  │
│  │ └─────────────┘ │  │ └─────────────┘ │                                  │
│  └─────────────────┘  └─────────────────┘                                  │
└─────────────────────────────────────────────────────────────────────────────┘
```
## Component Details

### 1. Mobile Application Layer
- **Framework**: React Native / Flutter / Native iOS/Android
- **Authentication**: OAuth2 client implementation
- **Video Player**: Custom player with progress tracking
- **Payment Integration**: PayPal, Naver Pay, Kakao Pay SDKs

### 1.1. Video Processing Pipeline
```
Original MP4 Upload → AWS MediaConvert → Multiple Outputs:
├── HLS Format (.m3u8 + .ts segments)
├── Multiple Bitrates (480p, 720p, 1080p)
├── Adaptive Streaming Ready
└── Thumbnail Generation
```

### 1.2. HLS (HTTP Live Streaming) Structure
```
movie_123/
├── master.m3u8          # Master playlist
├── 480p/
│   ├── playlist.m3u8    # 480p variant playlist
│   ├── segment_001.ts   # Video segments (10 seconds each)
│   ├── segment_002.ts
│   └── ...
├── 720p/
│   ├── playlist.m3u8
│   ├── segment_001.ts
│   └── ...
└── 1080p/
    ├── playlist.m3u8
    ├── segment_001.ts
    └── ...
```

### 2. API Gateway
- **Service**: AWS API Gateway
- **Features**: Rate limiting, CORS, request routing, authentication

### 3. Backend Services (AWS Lambda)

#### Auth Service
```javascript
// OAuth2 providers supported
const OAUTH_PROVIDERS = {
  GOOGLE: 'google',
  FACEBOOK: 'facebook',
  APPLE: 'apple'
};
```

#### Movie Service
```javascript
// Movie access control
const ACCESS_LEVELS = {
  PREVIEW: 0.1,    // 10% free access
  FULL: 1.0,       // Full access after payment
  COMMENT: 0.9,    // 90% to unlock comments
  SUPPORT: 0.9     // 90% to unlock support
};
```

### 4. Database Schema (DynamoDB)

#### Users Table
```json
{
  "user_id": "string (PK)",
  "email": "string",
  "oauth_provider": "string",
  "oauth_id": "string",
  "created_at": "timestamp",
  "last_login": "timestamp"
}
```

#### Movies Table
```json
{
  "movie_id": "string (PK)",
  "title": "string",
  "description": "string",
  "duration": "number",
  "s3_url": "string",
  "thumbnail_url": "string",
  "price": "number",
  "created_at": "timestamp"
}
```

#### Watch_History Table
```json
{
  "user_id": "string (PK)",
  "movie_id": "string (SK)",
  "progress_percentage": "number",
  "last_watched": "timestamp",
  "total_watch_time": "number"
}
```

#### Purchases Table
```json
{
  "purchase_id": "string (PK)",
  "user_id": "string",
  "movie_id": "string",
  "payment_method": "string",
  "amount": "number",
  "transaction_id": "string",
  "purchased_at": "timestamp"
}
```

#### Comments Table
```json
{
  "comment_id": "string (PK)",
  "movie_id": "string (GSI)",
  "user_id": "string",
  "content": "string",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

### 5. External Integrations

#### Payment Providers
- **PayPal**: REST API v2
- **Naver Pay**: Naver Pay API
- **Kakao Pay**: Kakao Pay API

#### OAuth2 Providers
- Google OAuth2
- Facebook Login
- Apple Sign In

## Security Considerations

1. **JWT Tokens**: Short-lived access tokens with refresh tokens
2. **API Rate Limiting**: Prevent abuse and ensure fair usage
3. **HTTPS Only**: All communications encrypted
4. **Input Validation**: Sanitize all user inputs
5. **CORS Configuration**: Restrict cross-origin requests

## Scalability Features

1. **CDN**: CloudFront for global movie delivery
2. **Caching**: ElastiCache for session and metadata caching
3. **Auto-scaling**: Lambda functions scale automatically
4. **Database**: DynamoDB with on-demand scaling

## Monitoring & Analytics

1. **CloudWatch**: Application and infrastructure monitoring
2. **User Analytics**: Watch patterns, popular content
3. **Payment Analytics**: Revenue tracking, conversion rates
4. **Error Tracking**: Application error monitoring

## Deployment Architecture

```
Development → Staging → Production
     │           │          │
     ▼           ▼          ▼
   Dev AWS    Stage AWS   Prod AWS
   Account    Account     Account
```
