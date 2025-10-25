# Workalot - Work Management App
## Project Planning Document

### Overview
A comprehensive work management application built with Flutter and Dart that helps users track work hours, manage tasks, schedule events, and calculate salaries. The app uses Firebase for cloud storage, authentication, and real-time synchronization across devices with offline-first capabilities.

---

## Core Features

### 1. User Authentication & Profile Management
**Functionality:**
- Email/password signup and login
- Google Sign-In integration
- Apple Sign-In integration
- Password reset and recovery
- Optional email verification (encouraged but not required for immediate access)
- Optional profile setup (skippable, can be completed later in Settings)
- Profile management:
  - Display name
  - Profile photo (auto-compressed, stored in Firebase Storage)
  - Job title
  - Email management
- Account settings and deletion
- Automatic auth state persistence
- Multi-device synchronization

**Data Models:**
- `UserProfile`: userId, email, displayName, photoUrl, jobTitle, createdAt, updatedAt

---

### 2. Hours Worked Tracking & Salary Calculation
**Functionality:**
- Clock in/out system for tracking work sessions
- Manual time entry for past work sessions
- Offline work session tracking with clear sync status indicator
- Weekly/monthly/yearly time summaries
- Hourly rate configuration
- Automatic salary calculation based on tracked hours
- Overtime tracking and calculation
- Export reports (PDF/CSV)
- Work history view with calendar integration
- Real-time synchronization across devices

**Data Models:**
- `WorkSession`: userId, startTime, endTime, duration, date, notes, syncStatus
- `SalarySettings`: userId, hourlyRate, overtimeRate, currency
- `WorkReport`: totalHours, regularHours, overtimeHours, totalSalary

---

### 3. Todo List Management
**Functionality:**
- Create, edit, and delete tasks
- Task status management:
  - Not Yet Scheduled
  - Currently Working On
  - Completed
  - Canceled
- Task properties:
  - Title and description
  - Priority levels (High, Medium, Low)
  - Deadline with time
  - Tags/categories
  - Subtasks with individual deadlines
  - Progress tracking
  - File attachments (up to 10MB via Firebase Storage)
- Subtask management:
  - Add/remove subtasks
  - Individual status for each subtask
  - Deadline tracking per subtask
  - Progress percentage based on completed subtasks
- Filtering and sorting:
  - By status
  - By priority
  - By deadline
  - By category
- Search functionality
- Real-time updates across devices
- Offline task management with automatic sync

**Data Models:**
- `Task`: id, userId, title, description, status, priority, deadline, createdAt, updatedAt, categoryId, attachmentUrls
- `SubTask`: id, taskId, userId, title, status, deadline, createdAt, updatedAt
- `TaskStatus`: enum (notScheduled, inProgress, completed, canceled)
- `Priority`: enum (low, medium, high)

---

### 4. Calendar & Events
**Functionality:**
- Month/week/day views
- Event management:
  - Create, edit, delete events
  - Event types: Meeting, Out of Office, Vacation, Personal, Other
  - Event properties: title, description, start/end time, location, attendees
  - Color coding by event type
- Integration with tasks (show task deadlines)
- Integration with work sessions
- Recurring events support
- Notification system:
  - Hybrid push and local notifications
  - Customizable reminders per event
  - Multiple reminders per event
  - Toggle notifications on/off per event
  - Cloud-triggered notifications via Firebase Cloud Messaging
- Real-time event synchronization

**Data Models:**
- `Event`: id, userId, title, description, eventType, startTime, endTime, location, color, notificationsEnabled
- `EventType`: enum (meeting, outOfOffice, vacation, personal, other)
- `Reminder`: id, eventId, userId, minutesBefore, enabled

---

### 5. Settings & Options
**Functionality:**
- Profile settings:
  - Name, photo upload (auto-compressed to 1024x1024), job title
  - Email management
  - Account deletion
- Work settings:
  - Hourly rate configuration
  - Overtime threshold
  - Currency selection
  - Work schedule (default hours)
- Notification settings:
  - Global notification toggle
  - Notification sound
  - Notification time defaults
- Display settings:
  - Theme selection (Light/Dark/System)
  - Language (for future expansion)
  - Date/time format
  - Week start day
- Data management:
  - Export data (PDF/CSV)
  - Clear all data
  - Sync status
- App information:
  - Version
  - About
  - Privacy policy

---

### 6. Theme System
**Implementation:**
- Light theme
- Dark theme
- System default (auto-switch based on device settings)
- Consistent color palette across app
- Smooth theme transitions
- Persistent theme preference (synced via Firestore)

---

## Technical Architecture

### State Management
**Recommended: Riverpod or Bloc**
- Riverpod: Modern, compile-safe, testable, excellent for Firebase streams
- Bloc: Event-driven, good for complex state management

### Database
**Cloud Firestore**
- NoSQL cloud database
- Real-time data synchronization
- Offline persistence enabled (~100MB cache)
- Automatic conflict resolution
- Real-time streams via `StreamProvider`
- Optimistic updates for better UX

**Firestore Structure:**
```
users/{userId}/
  ├── profile (document)
  ├── workSessions/{sessionId} (collection)
  ├── tasks/{taskId} (collection)
  │   └── subtasks/{subtaskId} (subcollection)
  ├── events/{eventId} (collection)
  │   └── reminders/{reminderId} (subcollection)
  └── settings/preferences (document)
```

### Authentication
**Firebase Authentication**
- Email/password authentication
- Google Sign-In integration
- Apple Sign-In integration
- Optional email verification (encouraged but not blocking)
- Password reset functionality
- Automatic auth state persistence
- Secure token management
- Account linking between providers

### Storage
**Firebase Storage**
- Profile photo storage (auto-compressed to max 1024x1024px)
- Task attachment storage (max 10MB per file)
- Secure, authenticated access
- Upload progress tracking
- Automatic cleanup of unused files
- CDN-backed for fast delivery

### Navigation
**GoRouter or Navigator 2.0**
- Deep linking support
- Type-safe routing
- Nested navigation
- Auth-guarded routes
- Redirect logic for unauthenticated users

### Notifications
**Hybrid Approach: Firebase Cloud Messaging + flutter_local_notifications**
- Firebase Cloud Messaging receives push notifications from server
- Flutter Local Notifications displays custom-styled notifications
- Background message handling
- Foreground message handling
- Scheduled local notifications for reminders
- Custom notification channels
- Cloud Functions trigger scheduled notifications

### UI Components
**Material Design 3**
- Modern, beautiful UI
- Consistent design language
- Responsive layouts
- Adaptive components

---

## Firebase Setup & Configuration

### Firebase Console Setup

1. **Create Firebase Project**
   - Go to [Firebase Console](https://console.firebase.google.com)
   - Create new project
   - Enable Google Analytics (optional)

2. **Register Apps**
   - Add Android app with package name
   - Add iOS app with bundle ID
   - Download configuration files

3. **Enable Services**
   - **Authentication:**
     - Enable Email/Password provider
     - Enable Google Sign-In provider
     - Enable Apple Sign-In provider (iOS)
     - Configure OAuth consent screen
   - **Firestore:**
     - Create database in production mode
     - Choose region closest to users
     - Set up security rules (see below)
   - **Storage:**
     - Enable Firebase Storage
     - Set up security rules (see below)
   - **Cloud Messaging:**
     - Enable Firebase Cloud Messaging
     - Generate server key for push notifications

### FlutterFire CLI Setup

```powershell
# Install Firebase CLI
npm install -g firebase-tools

# Install FlutterFire CLI
dart pub global activate flutterfire_cli

# Login to Firebase
firebase login

# Configure Flutter app
flutterfire configure
```

### Configuration Files

**Android:** `android/app/google-services.json`
- Downloaded from Firebase Console
- Contains API keys and project identifiers

**iOS:** `ios/Runner/GoogleService-Info.plist`
- Downloaded from Firebase Console
- Contains iOS-specific configuration

**Flutter:** `lib/firebase_options.dart`
- Auto-generated by FlutterFire CLI
- Platform-specific Firebase configuration

### Platform-Specific Configuration

**Android (Google Sign-In):**
- Add SHA-1 and SHA-256 certificates to Firebase Console
- Configure OAuth consent screen

**iOS (Apple Sign-In):**
- Enable "Sign in with Apple" capability in Xcode
- Configure Apple Sign-In in Firebase Console

### Firestore Security Rules

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // User data isolation
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### Firebase Storage Security Rules

```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    // User profile photos
    match /users/{userId}/profile/{filename} {
      allow read: if request.auth != null;
      allow write: if request.auth != null 
                   && request.auth.uid == userId
                   && request.resource.size < 10 * 1024 * 1024
                   && request.resource.contentType.matches('image/.*');
    }
    
    // Task attachments
    match /users/{userId}/attachments/{filename} {
      allow read: if request.auth != null && request.auth.uid == userId;
      allow write: if request.auth != null 
                   && request.auth.uid == userId
                   && request.resource.size < 10 * 1024 * 1024;
    }
  }
}
```

### Offline Persistence Configuration

```dart
// In main.dart or firebase_service.dart
await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);

// Enable Firestore offline persistence
FirebaseFirestore.instance.settings = const Settings(
  persistenceEnabled: true,
  cacheSizeBytes: Settings.CACHE_SIZE_UNLIMITED, // or specify size
);
```

---

## Project Structure

```
lib/
├── main.dart
├── app.dart
├── firebase_options.dart (auto-generated)
│
├── core/
│   ├── constants/
│   │   ├── app_colors.dart
│   │   ├── app_strings.dart
│   │   ├── app_routes.dart
│   │   └── firebase_constants.dart
│   ├── theme/
│   │   ├── app_theme.dart
│   │   ├── light_theme.dart
│   │   └── dark_theme.dart
│   ├── utils/
│   │   ├── date_utils.dart
│   │   ├── currency_formatter.dart
│   │   ├── validators.dart
│   │   └── image_compressor.dart
│   └── services/
│       ├── firebase_service.dart (Firebase initialization)
│       ├── firestore_service.dart (Firestore helpers)
│       ├── auth_service.dart (Authentication with social providers)
│       ├── storage_service.dart (File upload/download with compression)
│       ├── notification_service.dart (Hybrid FCM + local notifications)
│       └── export_service.dart
│
├── data/
│   ├── models/
│   │   ├── user_profile.dart
│   │   ├── task.dart
│   │   ├── subtask.dart
│   │   ├── work_session.dart
│   │   ├── event.dart
│   │   ├── reminder.dart
│   │   └── settings.dart
│   ├── repositories/
│   │   ├── auth_repository.dart
│   │   ├── user_repository.dart
│   │   ├── task_repository.dart (Firestore operations)
│   │   ├── work_session_repository.dart (Firestore operations)
│   │   ├── event_repository.dart (Firestore operations)
│   │   └── settings_repository.dart (Firestore operations)
│   └── providers/
│       ├── auth_provider.dart
│       ├── user_provider.dart
│       ├── task_provider.dart (StreamProvider)
│       ├── work_session_provider.dart (StreamProvider)
│       ├── event_provider.dart (StreamProvider)
│       ├── settings_provider.dart
│       └── theme_provider.dart
│
├── presentation/
│   ├── screens/
│   │   ├── auth/
│   │   │   ├── login_screen.dart
│   │   │   ├── signup_screen.dart
│   │   │   ├── forgot_password_screen.dart
│   │   │   ├── email_verification_screen.dart
│   │   │   ├── profile_setup_screen.dart
│   │   │   └── widgets/
│   │   │       ├── social_sign_in_button.dart
│   │   │       └── auth_text_field.dart
│   │   ├── home/
│   │   │   ├── home_screen.dart
│   │   │   └── widgets/
│   │   ├── time_tracking/
│   │   │   ├── time_tracking_screen.dart
│   │   │   ├── clock_in_out_screen.dart
│   │   │   ├── work_history_screen.dart
│   │   │   └── widgets/
│   │   │       └── sync_status_indicator.dart
│   │   ├── tasks/
│   │   │   ├── tasks_screen.dart
│   │   │   ├── task_detail_screen.dart
│   │   │   ├── add_edit_task_screen.dart
│   │   │   └── widgets/
│   │   ├── calendar/
│   │   │   ├── calendar_screen.dart
│   │   │   ├── event_detail_screen.dart
│   │   │   ├── add_edit_event_screen.dart
│   │   │   └── widgets/
│   │   └── settings/
│   │       ├── settings_screen.dart
│   │       ├── profile_settings_screen.dart
│   │       ├── work_settings_screen.dart
│   │       └── widgets/
│   └── widgets/
│       ├── custom_app_bar.dart
│       ├── custom_button.dart
│       ├── custom_card.dart
│       ├── custom_text_field.dart
│       └── loading_indicator.dart
│
└── l10n/ (for future localization)
```

---

## Required Dependencies

```yaml
dependencies:
  flutter:
    sdk: flutter
  
  # State Management
  flutter_riverpod: ^2.4.0  # or bloc: ^8.1.0
  
  # Firebase Core
  firebase_core: ^2.24.2
  
  # Firebase Services
  cloud_firestore: ^4.13.6
  firebase_auth: ^4.15.3
  firebase_storage: ^11.5.6
  firebase_messaging: ^14.7.9
  
  # Social Authentication
  google_sign_in: ^6.1.5
  sign_in_with_apple: ^5.0.0
  
  # UI & Theming
  google_fonts: ^6.1.0
  flutter_svg: ^2.0.9
  
  # Calendar
  table_calendar: ^3.0.9
  
  # Notifications
  flutter_local_notifications: ^16.3.0
  timezone: ^0.9.2
  
  # Date & Time
  intl: ^0.18.1
  
  # Image Handling
  image_picker: ^1.0.4
  image: ^4.1.3
  
  # Utilities
  uuid: ^4.2.1
  equatable: ^2.0.5
  
  # Export & Reports
  pdf: ^3.10.0
  csv: ^5.1.1
  share_plus: ^7.2.0
  
dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.0
```

---

## UI/UX Design Guidelines

### Design Principles
1. **Beautiful Design**: Modern, elegant and beatiful UI principles and design.
2. **Simplicity**: Clean, uncluttered interfaces
3. **Consistency**: Uniform design language throughout
4. **Accessibility**: High contrast, readable fonts, proper spacing
5. **Responsiveness**: Smooth animations and transitions
6. **Feedback**: Clear user feedback for all actions, especially sync status

### Color Scheme Ideas

**Light Theme:**
- Primary: Blue (#2196F3)
- Secondary: Teal (#009688)
- Accent: Orange (#FF9800)
- Background: White (#FFFFFF)
- Surface: Light Gray (#F5F5F5)
- Error: Red (#F44336)
- Success: Green (#4CAF50)
- Warning: Amber (#FFC107)

**Dark Theme:**
- Primary: Light Blue (#64B5F6)
- Secondary: Teal (#4DB6AC)
- Accent: Orange (#FFB74D)
- Background: Dark Gray (#121212)
- Surface: Dark Surface (#1E1E1E)
- Error: Light Red (#EF5350)
- Success: Light Green (#81C784)
- Warning: Light Amber (#FFD54F)

### Typography
- Headings: Bold, larger font sizes
- Body: Regular weight, comfortable reading size (16sp)
- Captions: Smaller, lighter weight for secondary info
- Buttons: Medium weight, uppercase

### Screen Layouts

**Authentication Screens:**
- **Login Screen:**
  - Email and password fields
  - "Sign in with Google" button
  - "Sign in with Apple" button
  - "Forgot Password?" link
  - "Create Account" navigation
  - Error messages for failed social sign-in
- **Signup Screen:**
  - Email, password, and confirm password fields
  - Social sign-in buttons
  - Terms and conditions checkbox
  - "Already have an account?" link
- **Email Verification Screen:**
  - Optional dismissible dialog
  - "Verify Email" and "Skip for Now" buttons
  - Resend verification link option
- **Profile Setup Screen:**
  - Optional and skippable
  - Photo picker with auto-compression preview
  - Name, job title, and hourly rate fields
  - "Complete Profile" and "Skip" buttons

**Home Screen:**
- Dashboard with quick stats
- Active work session indicator with sync status
- Today's tasks summary
- Upcoming events
- Quick action buttons
- Sync status indicator

**Time Tracking Screen:**
- Large clock in/out button
- Current session timer
- Offline sync status indicator (visible when offline)
- Today's total hours
- Weekly/monthly summary cards
- Salary calculation display

**Tasks Screen:**
- Filterable task list
- Status badges (color-coded)
- Priority indicators
- Deadline countdown
- Attachment indicators
- Floating action button to add task
- Pull to refresh

**Calendar Screen:**
- Month/week/day toggle
- Event markers
- Integration with tasks
- Quick event creation
- Sync indicator

**Settings Screen:**
- Grouped settings sections
- Profile photo with upload option
- Toggle switches
- Clear hierarchy
- Logout button
- Account deletion option

---

## Development Phases

### Phase 1: Foundation & Authentication (Week 1-2)
- **Project Setup:**
  - Create Flutter project
  - Create Firebase project in console
  - Run `flutterfire configure`
  - Add all dependencies
  - Configure Android SHA certificates
  - Configure iOS capabilities
- **Firebase Initialization:**
  - Initialize Firebase in `main.dart`
  - Set up Firestore offline persistence
  - Configure Firebase services
- **Authentication Implementation:**
  - Create auth service with email/password
  - Implement Google Sign-In with error handling
  - Implement Apple Sign-In with error handling
  - Create login screen
  - Create signup screen
  - Create forgot password screen
  - Create optional email verification prompt (dismissible)
  - Create optional profile setup screen (skippable)
  - Implement auth state management with Riverpod/Bloc
- **Navigation:**
  - Set up GoRouter with auth guards
  - Implement redirect logic for unauthenticated users
  - Create route structure
- **Theme System:**
  - Implement light and dark themes
  - Create theme provider synced with Firestore
  - Add theme toggle in settings
- **Core Models:**
  - Create all model classes with Firebase serialization
  - Add `toFirestore()` and `fromFirestore()` methods
  - Add `userId` field to all models
  - Add `syncStatus` to WorkSession model

### Phase 2: Time Tracking (Week 3)
- **Firestore Integration:**
  - Set up work session repository with Firestore operations
  - Implement real-time streams with StreamProvider
  - Add userId to all queries
- **UI Implementation:**
  - Work session tracking UI
  - Clock in/out functionality with offline support
  - Sync status indicator for active sessions
  - Visual feedback when offline (e.g., "Will sync when online")
  - Salary calculation logic
  - Work history display with real-time updates
- **Offline Support:**
  - Test offline clock in/out
  - Verify automatic sync when online
  - Add sync status to UI

### Phase 3: Task Management (Week 4-5)
- **Firestore Integration:**
  - Task repository with Firestore operations
  - Subtask subcollection management
  - Real-time task streams
  - User-scoped queries with userId
- **UI Implementation:**
  - Task CRUD operations
  - Subtask management
  - Task filtering and sorting
  - Task detail view
  - Real-time task updates
- **Storage Integration:**
  - Task attachment upload (10MB validation)
  - Image compression for image attachments
  - Upload progress indicators
  - Attachment download and viewing

### Phase 4: Calendar & Events (Week 6)
- **Firestore Integration:**
  - Event repository with Firestore operations
  - Reminder subcollection management
  - Real-time event streams
  - User-scoped queries
- **UI Implementation:**
  - Calendar UI (month/week/day views)
  - Event CRUD operations
  - Event-task integration
  - Recurring events support
  - Real-time event updates

### Phase 5: Notifications (Week 7)
- **Firebase Cloud Messaging:**
  - FCM token registration
  - Background message handler
  - Foreground message handler
  - Token refresh handling
- **Local Notifications:**
  - flutter_local_notifications setup
  - Custom notification channels
  - Notification styling
  - Integration with FCM
- **Cloud Functions (Optional):**
  - Set up Cloud Functions for scheduled notifications
  - Implement event reminder triggers
  - Implement task deadline notifications
- **UI Integration:**
  - Notification settings
  - Event reminder configuration
  - Test notification flow

### Phase 6: Settings & Polish (Week 8)
- **Firebase Storage:**
  - Profile photo upload with auto-compression to 1024x1024
  - Image picker integration
  - Upload progress indicator
  - Photo display and caching
- **Settings Screens:**
  - Profile settings with photo upload
  - Work settings
  - Notification settings
  - Display settings
  - Account management (logout, delete account)
- **Data Export:**
  - PDF report generation
  - CSV export functionality
  - Share functionality
- **UI/UX Polish:**
  - Loading states
  - Error handling and user feedback
  - Empty states
  - Smooth animations
  - Bug fixes

### Phase 7: Testing & Launch (Week 9-10)
- **Comprehensive Testing:**
  - Unit tests for models and repositories
  - Widget tests for UI components
  - Integration tests for user flows
  - Firebase Emulator testing
  - Offline functionality testing
  - Social sign-in testing on real devices
- **Performance Optimization:**
  - Optimize Firestore queries
  - Minimize unnecessary reads
  - Implement proper listener disposal
  - Test with large datasets
  - Monitor Firebase usage
- **Security:**
  - Review and test Firestore security rules
  - Review and test Storage security rules
  - Test user data isolation
  - Verify file size limits
- **Documentation:**
  - User documentation
  - API documentation
  - Setup instructions
  - Privacy policy
- **Deployment Preparation:**
  - App store assets
  - App store listings
  - Beta testing
  - Final testing

---

## Testing Strategy

### Unit Tests
- Model classes with Firebase serialization
- Repository logic (Firestore operations)
- Utility functions (image compression, validators)
- Calculation logic (salary, overtime)
- Auth service logic

### Widget Tests
- Individual widgets
- Screen layouts
- Authentication flows
- User interactions
- Loading and error states

### Integration Tests
- Complete user flows (signup to task creation)
- Firestore operations with Firebase Emulator
- Authentication flows with test accounts
- Offline/online transitions
- File upload and download
- Navigation

### Firebase Emulator Testing
- Use Firebase Emulator Suite for local testing
- Test Firestore security rules
- Test Storage security rules
- Test Cloud Functions (if implemented)

---

## Future Enhancements (Post-Launch)

1. **Cloud Functions**
   - Server-side salary calculations and aggregations
   - Automated scheduled notifications via FCM
   - User data aggregation for analytics
   - Automated backups to Cloud Storage
   - Cleanup of old or unused data

2. **Advanced Authentication**
   - Two-factor authentication (2FA)
   - Biometric authentication (fingerprint, Face ID)
   - Enhanced account recovery options
   - Session management and device tracking

3. **Team Features**
   - Shared calendars with team members
   - Team task assignments
   - Collaboration tools (comments, mentions)
   - Role-based access control

4. **Advanced Analytics**
   - Productivity insights dashboard
   - Time tracking analytics and trends
   - Visual reports and charts
   - Export analytics reports

5. **Integrations**
   - Google Calendar sync
   - Email integration
   - Third-party app connections (Slack, Trello, etc.)
   - Webhook support

6. **AI Features**
   - Smart task scheduling based on patterns
   - Time estimation using ML
   - Productivity suggestions
   - Automated task categorization

7. **Localization**
   - Multiple language support
   - Regional date/time formats
   - Currency localization

---

## Getting Started

### Prerequisites
- Flutter SDK (latest stable version)
- Dart SDK
- Android Studio / VS Code with Flutter extensions
- Git
- Firebase account (free tier)
- Node.js (for Firebase CLI)
- Xcode (for iOS development on macOS)

### Initial Setup Steps

1. **Create Flutter Project**
   ```powershell
   flutter create workalot
   cd workalot
   ```

2. **Create Firebase Project**
   - Go to [Firebase Console](https://console.firebase.google.com)
   - Click "Add Project"
   - Follow the setup wizard
   - Enable Google Analytics (optional)

3. **Install Firebase CLI and FlutterFire CLI**
   ```powershell
   npm install -g firebase-tools
   dart pub global activate flutterfire_cli
   firebase login
   ```

4. **Configure Firebase for Flutter**
   ```powershell
   flutterfire configure
   ```
   - Select your Firebase project
   - Select platforms (Android, iOS)
   - This generates `firebase_options.dart` and updates config files

5. **Configure Android (Google Sign-In)**
   - Generate SHA-1 and SHA-256 certificates:
     ```powershell
     cd android
     ./gradlew signingReport
     ```
   - Add certificates to Firebase Console (Project Settings > Your apps)
   - Configure OAuth consent screen in Google Cloud Console

6. **Configure iOS (Apple Sign-In)**
   - Open `ios/Runner.xcworkspace` in Xcode
   - Select Runner target > Signing & Capabilities
   - Add "Sign in with Apple" capability
   - Configure Apple Sign-In in Firebase Console

7. **Add Dependencies**
   - Add all dependencies from the Required Dependencies section to `pubspec.yaml`
   - Run `flutter pub get`

8. **Set Up Project Structure**
   - Create folder structure as defined in Project Structure section
   - Set up core constants, themes, and utilities

9. **Initialize Firebase**
   - Update `main.dart` to initialize Firebase
   - Enable Firestore offline persistence
   - Set up auth state listener

10. **Implement Authentication Flow**
    - Create auth service with email/password and social sign-in
    - Create authentication screens (login, signup, etc.)
    - Implement auth state management
    - Set up auth guards in navigation

11. **Configure Firestore and Storage**
    - Set up Firestore security rules in Firebase Console
    - Set up Storage security rules in Firebase Console
    - Test rules with Firebase Emulator

12. **Configure Firebase Cloud Messaging**
    - Enable FCM in Firebase Console
    - Add FCM configuration to Android and iOS
    - Implement notification handlers

13. **Start Development**
    - Begin with Phase 1 development tasks
    - Test authentication on real devices
    - Verify Firebase connections

---

## Notes & Considerations

### Performance
- Optimize Firestore queries with proper indexing
- Use `limit()` for pagination with large lists
- Implement lazy loading where appropriate
- Cache frequently accessed data using offline persistence
- Close Firestore listeners when not needed (use dispose methods)
- Use `startAfter()` for pagination instead of `skip()`
- Monitor Firebase usage in console to avoid quota issues

### Security
- Implement robust Firestore security rules for user data isolation
- Implement Storage security rules with file size and type validation
- Validate all user inputs on client and consider Cloud Functions for server-side validation
- Never store sensitive data (like raw passwords) in Firestore
- Use Firebase Auth for secure authentication only
- Encrypt sensitive salary information if needed
- Regularly audit security rules

### Firebase-Specific Considerations

**Authentication:**
- Persist auth state automatically with Firebase Auth
- Handle token refresh (automatic with Firebase SDK)
- Provide clear error messages when social sign-in fails
- Test account linking between providers (same email)
- Handle edge cases (disabled accounts, email already in use)

**Firestore:**
- User data isolation pattern: `users/{userId}/**`
- Enable offline persistence for better UX
- Configure cache size (~100MB for work data)
- Use compound indexes for complex queries (auto-prompted by Firebase)
- Batch write operations when possible (up to 500 operations)
- Use transactions for critical operations (salary calculations)
- Monitor read/write operations to stay within free tier limits

**Storage:**
- Validate file size (<10MB) on client-side before upload
- Validate file types on client-side
- Auto-compress images to max 1024x1024 before upload
- Show upload progress indicators for better UX
- Handle network failures with retry mechanism
- Implement file cleanup for deleted tasks/users
- Use signed URLs for secure file access

**Cost Management:**
- Minimize unnecessary Firestore reads by using local cache
- Batch write operations to reduce write costs
- Close unused listeners promptly
- Use offline persistence to reduce redundant reads
- Monitor Firebase usage dashboard regularly
- Set up budget alerts in Firebase Console
- Free tier limits: 50K reads, 20K writes, 20K deletes per day

**Offline Functionality:**
- Enable Firestore offline persistence in initialization
- Show clear sync status indicators when offline
- Active work sessions display "Will sync when online" message
- Queue operations automatically (handled by Firebase)
- Automatic sync when connection restored
- Handle conflicts gracefully (last write wins by default)
- Test thoroughly in airplane mode

### Accessibility
- Support screen readers
- Ensure sufficient color contrast (WCAG AA minimum)
- Provide text alternatives for icons and images
- Support dynamic text sizing
- Keyboard navigation support
- Semantic labels for interactive elements

### Platform Considerations
- Android and iOS specific features
- Handle platform-specific permissions (camera, storage, notifications)
- Native notification handling per platform
- Platform-specific UI adjustments (Material vs Cupertino)
- Test on multiple device sizes and OS versions
- Handle Android back button behavior
- iOS safe area considerations

---

## Success Metrics

1. **Usability**: Intuitive navigation, < 3 taps to main features, clear sync indicators
2. **Performance**: < 3s app startup (including Firebase init), smooth 60fps animations
3. **Reliability**: < 0.1% crash rate, successful offline/online transitions
4. **User Satisfaction**: Elegant design, positive user feedback, seamless auth experience
5. **Firebase Efficiency**: Stay within free tier limits, < 1s average Firestore query time

---

## Resources

### Flutter & Dart
- [Flutter Documentation](https://docs.flutter.dev/)
- [Dart Language Tour](https://dart.dev/guides/language/language-tour)
- [Flutter Community Packages](https://pub.dev/)

### Firebase
- [Firebase Console](https://console.firebase.google.com)
- [FlutterFire Documentation](https://firebase.flutter.dev/)
- [Cloud Firestore Documentation](https://firebase.google.com/docs/firestore)
- [Firebase Authentication Documentation](https://firebase.google.com/docs/auth)
- [Firebase Storage Documentation](https://firebase.google.com/docs/storage)
- [Firebase Cloud Messaging Documentation](https://firebase.google.com/docs/cloud-messaging)
- [Cloud Functions for Firebase](https://firebase.google.com/docs/functions)

### Authentication
- [Google Sign-In for Flutter](https://pub.dev/packages/google_sign_in)
- [Sign in with Apple for Flutter](https://pub.dev/packages/sign_in_with_apple)

### UI/UX
- [Material Design 3](https://m3.material.io/)
- [Flutter Widget Catalog](https://docs.flutter.dev/development/ui/widgets)

### State Management
- [Riverpod Documentation](https://riverpod.dev/)
- [Bloc Documentation](https://bloclibrary.dev/)

### Other Tools
- [Firebase Emulator Suite](https://firebase.google.com/docs/emulator-suite)
- [FlutterFire CLI](https://firebase.flutter.dev/docs/cli/)

---

**Last Updated**: October 23, 2025  
**Version**: 2.0  
**Status**: Planning Phase - Firebase Migration Complete
