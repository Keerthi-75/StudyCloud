# Firebase Deployment Instructions

This guide will help you deploy your StudyCloud platform with Firebase services including Firestore, Storage, and Authentication.

## 📋 Prerequisites

1. **Node.js** (v16 or higher)
2. **Firebase CLI** installed globally
3. **Firebase Project** created
4. **Environment variables** configured

## 🚀 Quick Setup

### 1. Install Firebase CLI

```bash
npm install -g firebase-tools
```

### 2. Login to Firebase

```bash
firebase login
```

### 3. Initialize Firebase in Your Project

```bash
# Navigate to your project directory
cd your-studycloud-project

# Initialize Firebase
firebase init
```

**Select the following services:**
- ✅ Firestore: Configure security rules and indexes
- ✅ Storage: Configure security rules for Cloud Storage
- ✅ Hosting: Configure files for Firebase Hosting (optional)

**Configuration choices:**
- Use existing project: Select your StudyCloud project
- Firestore rules file: `firebase-firestore.rules`
- Firestore indexes file: `firestore.indexes.json`
- Storage rules file: `firebase-storage.rules`
- Public directory: `dist` (for Vite build output)

## 🔧 Configuration Files

### Environment Variables (.env)

Ensure your `.env` file contains:

```env
# Firebase Configuration
VITE_FIREBASE_API_KEY=your_api_key_here
VITE_FIREBASE_AUTH_DOMAIN=your-project.firebaseapp.com
VITE_FIREBASE_PROJECT_ID=your-project-id
VITE_FIREBASE_STORAGE_BUCKET=your-project.firebasestorage.app
VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
VITE_FIREBASE_APP_ID=your_app_id
VITE_FIREBASE_MEASUREMENT_ID=your_measurement_id

# Gemini AI (Optional)
VITE_GEMINI_API_KEY=your_gemini_api_key

# Supabase (Alternative/Additional)
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### Firebase Configuration (firebase.json)

Create or update `firebase.json`:

```json
{
  "firestore": {
    "rules": "firebase-firestore.rules",
    "indexes": "firestore.indexes.json"
  },
  "storage": {
    "rules": "firebase-storage.rules"
  },
  "hosting": {
    "public": "dist",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ],
    "headers": [
      {
        "source": "**/*.@(js|css)",
        "headers": [
          {
            "key": "Cache-Control",
            "value": "max-age=31536000"
          }
        ]
      }
    ]
  }
}
```

### Firestore Indexes (firestore.indexes.json)

Create `firestore.indexes.json`:

```json
{
  "indexes": [
    {
      "collectionGroup": "documents",
      "queryScope": "COLLECTION",
      "fields": [
        {
          "fieldPath": "groupId",
          "order": "ASCENDING"
        },
        {
          "fieldPath": "lastModified",
          "order": "DESCENDING"
        }
      ]
    },
    {
      "collectionGroup": "messages",
      "queryScope": "COLLECTION",
      "fields": [
        {
          "fieldPath": "groupId",
          "order": "ASCENDING"
        },
        {
          "fieldPath": "timestamp",
          "order": "ASCENDING"
        }
      ]
    },
    {
      "collectionGroup": "meetings",
      "queryScope": "COLLECTION",
      "fields": [
        {
          "fieldPath": "groupId",
          "order": "ASCENDING"
        },
        {
          "fieldPath": "startTime",
          "order": "ASCENDING"
        }
      ]
    },
    {
      "collectionGroup": "tasks",
      "queryScope": "COLLECTION",
      "fields": [
        {
          "fieldPath": "groupId",
          "order": "ASCENDING"
        },
        {
          "fieldPath": "status",
          "order": "ASCENDING"
        },
        {
          "fieldPath": "dueDate",
          "order": "ASCENDING"
        }
      ]
    }
  ],
  "fieldOverrides": []
}
```

## 🚀 Deployment Steps

### 1. Build Your Application

```bash
# Install dependencies
npm install

# Build for production
npm run build
```

### 2. Deploy Security Rules

```bash
# Deploy Firestore rules
firebase deploy --only firestore:rules

# Deploy Storage rules
firebase deploy --only storage

# Deploy both
firebase deploy --only firestore,storage
```

### 3. Deploy Application (Optional)

```bash
# Deploy to Firebase Hosting
firebase deploy --only hosting

# Or deploy everything
firebase deploy
```

## 🔐 Security Configuration

### Authentication Setup

1. **Enable Authentication Methods:**
   - Go to Firebase Console → Authentication → Sign-in method
   - Enable Email/Password authentication
   - Configure authorized domains

2. **Email Templates:**
   - Customize email verification templates
   - Set up password reset emails

### Firestore Security

The provided `firebase-firestore.rules` includes:

- ✅ **User Authentication**: All operations require authentication
- ✅ **Group-based Access**: Users can only access groups they're members of
- ✅ **Role-based Permissions**: Admins have additional privileges
- ✅ **Data Validation**: Ensures data integrity and structure
- ✅ **Collaborative Documents**: Secure real-time collaboration

### Storage Security

The provided `firebase-storage.rules` includes:

- ✅ **File Size Limits**: Configurable per file type
- ✅ **File Type Validation**: Prevents malicious uploads
- ✅ **Group-based Access**: Files are scoped to groups
- ✅ **Metadata Requirements**: Ensures proper file tracking

## 🧪 Testing

### 1. Test Security Rules

```bash
# Install Firebase emulator
npm install -g firebase-tools

# Start emulators
firebase emulators:start

# Run your application against emulators
npm run dev
```

### 2. Test File Uploads

1. Create a test group
2. Upload various file types
3. Verify permissions and access controls
4. Test collaborative document editing

### 3. Test Real-time Features

1. Open multiple browser windows
2. Join the same group
3. Test real-time chat
4. Test collaborative document editing

## 📊 Monitoring

### 1. Firebase Console

Monitor your application through:
- **Authentication**: User registrations and activity
- **Firestore**: Database usage and performance
- **Storage**: File uploads and bandwidth
- **Hosting**: Website traffic and performance

### 2. Performance Monitoring

Add Firebase Performance Monitoring:

```bash
npm install firebase
```

Update your `src/lib/firebase.ts`:

```typescript
import { getPerformance } from 'firebase/performance';

// Initialize Performance Monitoring
const perf = getPerformance(app);
```

## 🔧 Troubleshooting

### Common Issues

1. **Permission Denied Errors**
   - Check Firestore security rules
   - Verify user authentication
   - Ensure user is member of the group

2. **File Upload Failures**
   - Check Storage security rules
   - Verify file size and type limits
   - Check Firebase Storage quotas

3. **Real-time Updates Not Working**
   - Verify Firestore indexes are deployed
   - Check network connectivity
   - Ensure proper listener setup

### Debug Commands

```bash
# Check Firebase project status
firebase projects:list

# Validate security rules
firebase firestore:rules:get
firebase storage:rules:get

# Check deployment status
firebase deploy --dry-run
```

## 📈 Scaling Considerations

### Performance Optimization

1. **Firestore Optimization**
   - Use compound indexes for complex queries
   - Implement pagination for large datasets
   - Cache frequently accessed data

2. **Storage Optimization**
   - Implement file compression
   - Use appropriate file formats
   - Set up CDN for global distribution

3. **Real-time Features**
   - Limit concurrent connections
   - Implement connection pooling
   - Use offline persistence

### Cost Management

1. **Monitor Usage**
   - Set up billing alerts
   - Track read/write operations
   - Monitor storage usage

2. **Optimize Queries**
   - Minimize document reads
   - Use efficient query patterns
   - Implement proper caching

## 🎯 Production Checklist

- [ ] Environment variables configured
- [ ] Security rules deployed and tested
- [ ] Authentication methods enabled
- [ ] File upload limits configured
- [ ] Firestore indexes created
- [ ] Performance monitoring enabled
- [ ] Billing alerts set up
- [ ] Backup strategy implemented
- [ ] Error monitoring configured
- [ ] SSL certificates configured

## 📞 Support

For additional help:

1. **Firebase Documentation**: https://firebase.google.com/docs
2. **Firebase Support**: https://firebase.google.com/support
3. **Community Forums**: https://stackoverflow.com/questions/tagged/firebase

---

**🎉 Congratulations!** Your StudyCloud platform is now ready for production with Firebase!