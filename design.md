# E-Bhoomi - Agricultural IoT Dashboard
## Design Document

### Document Information
- **Version:** 1.0
- **Last Updated:** February 2026
- **Status:** Active Development

---

## 1. System Architecture

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Client Layer (Browser)                   │
│  ┌──────────────────────────────────────────────────────┐  │
│  │           Next.js 13 React Application                │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │  │
│  │  │  Landing   │  │ Dashboard  │  │  Balaram   │     │  │
│  │  │    Page    │  │   Pages    │  │     AI     │     │  │
│  │  └────────────┘  └────────────┘  └────────────┘     │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    API Integration Layer                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ThingSpeak│  │  Weather │  │  Gemini  │  │  Twilio  │  │
│  │   API    │  │   API    │  │    AI    │  │   SMS    │  │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                 │
│  │ OpenCage │  │   MQTT   │  │ Omnidim  │                 │
│  │Geocoding │  │  Broker  │  │Voice API │                 │
│  └──────────┘  └──────────┘  └──────────┘                 │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Data Sources Layer                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                 │
│  │   IoT    │  │  Weather │  │    AI    │                 │
│  │ Sensors  │  │ Services │  │  Models  │                 │
│  └──────────┘  └──────────┘  └──────────┘                 │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Component Architecture

```
app/
├── page.tsx (Landing Page)
├── layout.tsx (Root Layout)
├── dashboard/
│   ├── layout.tsx (Dashboard Layout with Sidebar)
│   ├── page.tsx (Main Dashboard)
│   ├── sensors/page.tsx (Sensor Analytics)
│   ├── alerts/page.tsx (Alert Center)
│   ├── recommendations/page.tsx (AI Recommendations)
│   ├── balaram-ai/
│   │   ├── page.tsx (AI Chat Interface)
│   │   ├── components/
│   │   │   ├── CameraPreview.tsx
│   │   │   └── Chatbot.tsx
│   │   └── services/
│   │       ├── geminiChatService.ts
│   │       ├── geminiWebSocket.ts
│   │       └── transcriptionService.ts
│   └── [other pages]
├── api/
│   ├── alerts/route.ts
│   ├── pump/route.ts
│   ├── ml/agriculture/recommendation/route.ts
│   └── omnidim/call/route.ts
lib/
├── thingspeak.ts (IoT Data Service)
├── weather.ts (Weather Service)
├── agricultureService.ts (AI Recommendation Service)
├── twilio.ts (SMS Service)
└── utils.ts (Utility Functions)
components/
├── ui/ (Reusable UI Components)
├── navigation/
│   └── header.tsx
└── sections/ (Landing Page Sections)
```

---

## 2. Data Flow Design

### 2.1 Sensor Data Flow

```
IoT Sensors → ThingSpeak Cloud → ThingSpeak API → Next.js API Routes
                                                          ↓
                                                   Data Processing
                                                          ↓
                                                   React Components
                                                          ↓
                                                   Recharts Visualization
```

### 2.2 AI Recommendation Flow

```
Sensor Data (ThingSpeak) → fetchLatestSensorData()
                                    ↓
                          Extract NPK, Temp, Humidity, Soil Moisture
                                    ↓
                          POST /api/ml/agriculture/recommendation
                                    ↓
                          AI Model Analysis (Gemini AI)
                                    ↓
                          Return Recommendation Object
                                    ↓
                          Display in Dashboard with Urgency Color
```

### 2.3 Pump Control Flow

```
User Action / Automatic Trigger → POST /api/pump
                                        ↓
                                  MQTT Message Published
                                        ↓
                                  IoT Device Receives Command
                                        ↓
                                  Pump State Changes
                                        ↓
                                  UI Updates (Optimistic)
```

### 2.4 Alert Flow

```
Sensor Data Update → Check Thresholds (>80% or <20% moisture)
                            ↓
                    POST /api/alerts
                            ↓
                    Twilio SMS API Call
                            ↓
                    SMS Sent to Farmer
                            ↓
                    Toast Notification in UI
```

---

## 3. Database Design

### 3.1 Current State (No Persistent Database)
The current implementation uses:
- **Client-side state:** React useState/useEffect hooks
- **Session storage:** Temporary data in component state
- **External storage:** ThingSpeak for historical sensor data

### 3.2 Future Database Schema (Planned)

#### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255),
  phone VARCHAR(20),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

#### Farms Table
```sql
CREATE TABLE farms (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  name VARCHAR(255) NOT NULL,
  location GEOGRAPHY(POINT),
  area_hectares DECIMAL(10,2),
  crop_type VARCHAR(100),
  created_at TIMESTAMP DEFAULT NOW()
);
```

#### Sensors Table
```sql
CREATE TABLE sensors (
  id UUID PRIMARY KEY,
  farm_id UUID REFERENCES farms(id),
  sensor_type VARCHAR(50), -- 'soil_moisture', 'npk', 'weather'
  thingspeak_channel_id VARCHAR(50),
  status VARCHAR(20) DEFAULT 'active',
  last_reading_at TIMESTAMP
);
```

#### Recommendations Table
```sql
CREATE TABLE recommendations (
  id UUID PRIMARY KEY,
  farm_id UUID REFERENCES farms(id),
  action VARCHAR(100),
  semantic_tag TEXT,
  confidence DECIMAL(3,2),
  sensor_data JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  status VARCHAR(20) DEFAULT 'pending' -- 'pending', 'completed', 'ignored'
);
```

#### Alerts Table
```sql
CREATE TABLE alerts (
  id UUID PRIMARY KEY,
  farm_id UUID REFERENCES farms(id),
  alert_type VARCHAR(50),
  message TEXT,
  severity VARCHAR(20), -- 'low', 'medium', 'high', 'critical'
  sent_at TIMESTAMP DEFAULT NOW(),
  acknowledged_at TIMESTAMP
);
```

---

## 4. API Design

### 4.1 Internal API Routes

#### GET /api/thingspeak/pump
**Purpose:** Read current pump state
**Response:**
```json
{
  "state": "on" | "off",
  "timestamp": "2026-02-15T10:30:00Z"
}
```

#### POST /api/pump
**Purpose:** Control irrigation pump
**Request:**
```json
{
  "state": "ON" | "OFF"
}
```
**Response:**
```json
{
  "success": true,
  "state": "on",
  "message": "Pump turned on successfully"
}
```

#### POST /api/alerts
**Purpose:** Send SMS alert
**Request:**
```json
{
  "soilMoisture": 85.5
}
```
**Response:**
```json
{
  "success": true,
  "message": "Alert sent successfully"
}
```

#### POST /api/ml/agriculture/recommendation
**Purpose:** Get AI-powered farming recommendation
**Request:**
```json
{
  "n": 45.2,
  "p": 38.7,
  "k": 52.1,
  "temperature": 28.5,
  "humidity": 65.3,
  "soil_moisture": 42.8
}
```
**Response:**
```json
{
  "action": "Apply Fertilizer",
  "semantic_tag": "Nitrogen levels are slightly low for optimal growth",
  "ndi_label": "Low",
  "pdi_label": "Adequate",
  "confidence": 0.87,
  "score": 0.92,
  "matched_record": { /* reference data */ }
}
```

#### POST /api/omnidim/call
**Purpose:** Initiate AI voice call
**Request:**
```json
{
  "to": "+919876543210",
  "call_context": {
    "customer_name": "Farmer Name",
    "account_id": "FARM-001",
    "priority": "high"
  }
}
```

### 4.2 External API Integrations

#### ThingSpeak API
- **Endpoint:** `https://api.thingspeak.com/channels/{CHANNEL_ID}/feeds.json`
- **Method:** GET
- **Parameters:** `api_key`, `results` (number of data points)
- **Rate Limit:** Free tier limits apply
- **Caching:** 30 seconds

#### Open-Meteo Weather API
- **Endpoint:** `https://api.open-meteo.com/v1/forecast`
- **Method:** GET
- **Parameters:** `latitude`, `longitude`, `hourly`, `current_weather`
- **Rate Limit:** No strict limits (free tier)
- **Caching:** 5 minutes

#### OpenCage Geocoding API
- **Endpoint:** `https://api.opencagedata.com/geocode/v1/json`
- **Method:** GET
- **Parameters:** `q` (coordinates), `key`, `language`
- **Rate Limit:** 2,500 requests/day (free tier)
- **Caching:** Session-based

#### Google Gemini AI API
- **Purpose:** Chat responses and recommendations
- **Model:** gemini-pro
- **Rate Limit:** As per API key tier
- **Streaming:** Supported for chat

#### Twilio SMS API
- **Purpose:** Send alert messages
- **Rate Limit:** As per account plan
- **Retry Logic:** 3 attempts with exponential backoff

---

## 5. UI/UX Design

### 5.1 Design System

#### Color Palette
```css
/* Primary Colors */
--green-600: #16A34A;
--green-700: #15803D;
--blue-600: #2563EB;
--blue-700: #1D4ED8;

/* Status Colors */
--red-500: #EF4444;    /* Danger/Urgent */
--yellow-500: #EAB308; /* Warning/Moderate */
--green-500: #22C55E;  /* Success/Good */

/* Neutral Colors */
--gray-50: #F9FAFB;
--gray-100: #F3F4F6;
--gray-500: #6B7280;
--gray-900: #111827;
```

#### Typography Scale
```css
/* Headings */
h1: 2.25rem (36px) - font-bold
h2: 1.5rem (24px) - font-semibold
h3: 1.25rem (20px) - font-semibold

/* Body */
body: 1rem (16px) - font-normal
small: 0.875rem (14px) - font-normal
xs: 0.75rem (12px) - font-normal
```

#### Spacing System
```css
/* Based on 4px base unit */
xs: 0.25rem (4px)
sm: 0.5rem (8px)
md: 1rem (16px)
lg: 1.5rem (24px)
xl: 2rem (32px)
2xl: 3rem (48px)
```

### 5.2 Component Design Patterns

#### Sensor Card Component
```typescript
interface SensorCardProps {
  title: string;
  value: string | number;
  unit: string;
  trend?: {
    change: number;
    increasing: boolean;
  };
}
```
**Visual Design:**
- White background with shadow
- Title at top left
- Large value display with unit
- Trend indicator (↑/↓) with percentage
- Status badge for moisture sensors
- Icon representation (optional)

#### Chart Component
```typescript
interface ChartProps {
  data: Array<{time: string, value: number}>;
  dataKey: string;
  color: string;
  unit: string;
  timeRange: string;
}
```
**Features:**
- Responsive container
- Cartesian grid
- X-axis: Time labels
- Y-axis: Value scale
- Tooltip on hover
- Legend
- Brush for zooming
- Time range selector

#### Recommendation Card
```typescript
interface RecommendationCardProps {
  action: string;
  semantic_tag: string;
  ndi_label: string;
  pdi_label: string;
  confidence: number;
  urgency: 'urgent' | 'moderate' | 'good';
}
```
**Visual Design:**
- Border color based on urgency
- Icon representing action type
- Confidence percentage
- Analysis section with semantic tag
- NDI/PDI badges
- Recommended action highlighted

### 5.3 Responsive Breakpoints
```css
/* Mobile First Approach */
sm: 640px   /* Small devices */
md: 768px   /* Tablets */
lg: 1024px  /* Laptops */
xl: 1280px  /* Desktops */
2xl: 1536px /* Large screens */
```

### 5.4 Navigation Design

#### Desktop Sidebar
- Width: 288px (expanded), 64px (collapsed)
- Fixed position
- Collapsible with toggle button
- Icons + labels (expanded) or icons only (collapsed)
- Active state highlighting
- Smooth transitions

#### Mobile Navigation
- Hamburger menu button
- Slide-in drawer from left
- Full-height overlay
- Touch-friendly tap targets (min 44px)
- Close on route change

---

## 6. State Management Design

### 6.1 Component State Structure

#### Dashboard Page State
```typescript
interface DashboardState {
  // Sensor Data
  weatherData: WeatherData;
  soilMoistureData: SoilMoistureData;
  npkData: NPKData;
  historyData: ThingSpeakData[];
  npkHistoryData: NPKData[];
  
  // UI State
  selectedRange: '1h' | '24h' | '7d' | '30d' | '1y';
  isLoading: boolean;
  
  // Location
  location: {
    latitude: number;
    longitude: number;
    locationName: string;
  };
  isLocating: boolean;
  
  // Trends
  soilMoistureTrend: TrendData;
  npkTrend: TrendData;
  
  // AI Recommendations
  agricultureRecommendation: AgricultureRecommendation | null;
  recommendationLoading: boolean;
}
```

#### Sensors Page State
```typescript
interface SensorsState {
  sensorHistory: ThingSpeakData[];
  npkHistory: NPKData[];
  selectedRange: string;
  isLoading: boolean;
  
  // Pump Control
  modeAutomatic: boolean;
  pumpState: 'on' | 'off' | 'unknown';
  isToggling: boolean;
}
```

### 6.2 Data Fetching Strategy

#### Polling Intervals
```typescript
const POLLING_INTERVALS = {
  '1h': 15000,    // 15 seconds
  '24h': 60000,   // 1 minute
  '7d': 300000,   // 5 minutes
  '30d': 900000,  // 15 minutes
  '1y': 3600000   // 1 hour
};
```

#### Cache Strategy
- **Sensor Data:** 30 seconds (Next.js revalidate)
- **Weather Data:** 5 minutes
- **AI Recommendations:** On-demand + 5 minute auto-refresh
- **Location Data:** Session-based

### 6.3 Error Handling Strategy

```typescript
// Error Boundary Pattern
try {
  const data = await fetchSensorData();
  setData(data);
} catch (error) {
  console.error('Error fetching data:', error);
  toast.error('Failed to fetch sensor data. Please try again.');
  // Fallback to cached data or default values
}
```

---

## 7. Security Design

### 7.1 API Key Management
- All API keys stored in environment variables
- Never exposed in client-side code
- Server-side API routes act as proxy
- Keys rotated periodically

### 7.2 Input Validation
```typescript
// Example: Phone number validation
const validatePhoneNumber = (input: string): boolean => {
  const digitsOnly = input.replace(/\D/g, '');
  return digitsOnly.length === 10;
};

// Example: Coordinate validation
const isValidCoordinate = (lat: number, lon: number): boolean => {
  return (
    !isNaN(lat) && !isNaN(lon) &&
    lat >= -90 && lat <= 90 &&
    lon >= -180 && lon <= 180 &&
    lat !== 0 && lon !== 0
  );
};
```

### 7.3 Rate Limiting
- Client-side debouncing for user actions
- Server-side rate limiting (future)
- Exponential backoff for retries

### 7.4 Data Sanitization
```typescript
const parseNumericValue = (
  value: string | null | undefined,
  fallback: number = 0
): number => {
  if (!value || value.trim() === '') return fallback;
  const parsed = parseFloat(value.trim());
  return isNaN(parsed) ? fallback : parsed;
};
```

---

## 8. Performance Optimization

### 8.1 Code Splitting
- Route-based code splitting (Next.js automatic)
- Dynamic imports for heavy components
- Lazy loading for charts and visualizations

### 8.2 Image Optimization
- Next.js Image component for automatic optimization
- Unoptimized mode for static export
- Proper sizing and formats

### 8.3 Bundle Optimization
```javascript
// next.config.js
module.exports = {
  eslint: {
    ignoreDuringBuilds: true,
  },
  images: { 
    unoptimized: true 
  },
  // Future: Add bundle analyzer
};
```

### 8.4 Rendering Strategy
- Client-side rendering for dashboard (real-time data)
- Static generation for landing page
- API routes for server-side logic

### 8.5 Data Optimization
- Efficient data structures
- Memoization for expensive calculations
- Pagination for large datasets (future)
- Virtual scrolling for long lists (future)

---

## 9. Testing Strategy

### 9.1 Unit Testing (Planned)
```typescript
// Example test structure
describe('agricultureService', () => {
  describe('getAgricultureRecommendation', () => {
    it('should return recommendation for valid sensor data', async () => {
      // Test implementation
    });
    
    it('should handle missing sensor data gracefully', async () => {
      // Test implementation
    });
  });
});
```

### 9.2 Integration Testing (Planned)
- API route testing
- External API mocking
- End-to-end user flows

### 9.3 Manual Testing Checklist
- [ ] Dashboard loads with all sensor data
- [ ] Charts render correctly for all time ranges
- [ ] Location detection works
- [ ] Pump control functions in manual mode
- [ ] Automatic pump control triggers correctly
- [ ] Alerts send SMS successfully
- [ ] AI recommendations display properly
- [ ] Chatbot responds to queries
- [ ] Mobile responsive design works
- [ ] Error states display correctly

---

## 10. Deployment Design

### 10.1 Build Process
```bash
npm run build  # Next.js production build
npm run start  # Start production server
```

### 10.2 Environment Configuration
```env
# Production
NODE_ENV=production
NEXT_PUBLIC_THINGSPEAK_CHANNEL_ID=xxxxx
NEXT_PUBLIC_THINGSPEAK_READ_API_KEY=xxxxx
# ... other variables
```

### 10.3 Hosting Options
- **Vercel:** Recommended (Next.js native)
- **Netlify:** Alternative
- **AWS Amplify:** Enterprise option
- **Self-hosted:** VPS with Node.js

### 10.4 CI/CD Pipeline (Future)
```yaml
# Example GitHub Actions workflow
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install dependencies
        run: npm ci
      - name: Build
        run: npm run build
      - name: Deploy
        run: npm run deploy
```

---

## 11. Monitoring and Logging

### 11.1 Client-Side Logging
```typescript
// Console logging for development
console.log('Fetching sensor data for range:', range);
console.error('Error fetching data:', error);

// Future: Sentry or similar for production
```

### 11.2 Performance Monitoring
- Core Web Vitals tracking (future)
- API response time monitoring
- Error rate tracking
- User engagement metrics

### 11.3 Analytics (Future)
- Google Analytics or Plausible
- Custom event tracking
- User journey analysis
- Feature usage statistics

---

## 12. Accessibility Design

### 12.1 Keyboard Navigation
- All interactive elements accessible via Tab
- Logical tab order
- Focus indicators visible
- Escape key closes modals/menus

### 12.2 Screen Reader Support
- Semantic HTML elements
- ARIA labels where needed
- Alt text for images
- Descriptive link text

### 12.3 Color Contrast
- WCAG AA compliance target
- Minimum 4.5:1 for normal text
- Minimum 3:1 for large text
- Status not conveyed by color alone

### 12.4 Responsive Text
- Scalable font sizes (rem units)
- Readable line lengths
- Adequate line height (1.5+)
- No horizontal scrolling

---

## 13. Internationalization (Future)

### 13.1 Language Support
- English (default)
- Hindi
- Regional languages (Tamil, Telugu, Bengali, etc.)

### 13.2 Implementation Strategy
```typescript
// Using next-i18next
import { useTranslation } from 'next-i18next';

const { t } = useTranslation('common');
<h1>{t('dashboard.welcome')}</h1>
```

### 13.3 Localization Considerations
- Date/time formats
- Number formats
- Currency (₹)
- Units (metric system)

---

## 14. Documentation

### 14.1 Code Documentation
- JSDoc comments for complex functions
- README files in major directories
- API documentation
- Component prop documentation

### 14.2 User Documentation (Future)
- User guide
- Video tutorials
- FAQ section
- Troubleshooting guide

### 14.3 Developer Documentation
- Setup instructions
- Architecture overview
- API integration guides
- Contribution guidelines

---

## 15. Version Control Strategy

### 15.1 Branch Strategy
```
main (production)
├── develop (integration)
│   ├── feature/sensor-analytics
│   ├── feature/ai-recommendations
│   └── bugfix/pump-control
```

### 15.2 Commit Convention
```
feat: Add pump automatic control
fix: Resolve location detection issue
docs: Update API documentation
style: Format code with Prettier
refactor: Simplify data fetching logic
test: Add unit tests for agricultureService
```

---

## Document Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Feb 2026 | Development Team | Initial design document |

---

## Appendix

### A. Technology Stack Summary
- **Frontend:** Next.js 13, React 18, TypeScript
- **Styling:** Tailwind CSS, Radix UI
- **Charts:** Recharts
- **State:** React Hooks
- **APIs:** ThingSpeak, Open-Meteo, Gemini AI, Twilio
- **Protocols:** REST, MQTT, WebSocket

### B. Key Dependencies
```json
{
  "next": "13.5.1",
  "react": "18.2.0",
  "typescript": "5.2.2",
  "tailwindcss": "3.3.3",
  "recharts": "2.12.7",
  "@google/generative-ai": "^0.21.0",
  "mqtt": "^5.14.1",
  "sonner": "^1.7.4"
}
```

### C. External Service Limits
- **ThingSpeak:** Free tier - 3 million messages/year
- **Open-Meteo:** No strict limits (fair use)
- **OpenCage:** 2,500 requests/day (free)
- **Gemini AI:** As per API key tier
- **Twilio:** As per account plan
