# Notification & Profile Bottom Sheets Implementation

## Overview
This document describes the implementation of notification and profile bottom sheets for the BrightCare Dental Patient Android app.

## Features Implemented

### 1. Notification Bottom Sheet
- Displays a list of notifications in a bottom sheet dialog
- Shows notification icon, message, and timestamp
- Different icons based on notification type (Info, Reminder, Confirmation)
- "View all notifications" footer button
- Close button in header
- Notification badge indicator on toolbar bell icon

### 2. Profile Menu Bottom Sheet
- Displays user profile menu options
- User avatar and name in header
- Three menu items:
  - View Profile
  - Settings
  - Logout (styled in red for danger action)
- Close button in header

### 3. Toolbar Integration
- App title on the left
- Notification bell icon with badge indicator
- Profile icon
- Material Design toolbar with elevation

## File Structure

```
app/src/main/
├── java/com/zeke/brightcaredentalpatient/
│   ├── MainActivity.kt (updated)
│   ├── data/models/
│   │   ├── Notification.kt (new)
│   │   └── ProfileMenuItem.kt (new)
│   └── ui/
│       ├── adapters/
│       │   ├── NotificationAdapter.kt (new)
│       │   └── ProfileMenuAdapter.kt (new)
│       └── components/
│           ├── NotificationBottomSheet.kt (new)
│           └── ProfileBottomSheet.kt (new)
└── res/
    ├── layout/
    │   ├── activity_main.xml (updated)
    │   ├── bottom_sheet_notification.xml (new)
    │   ├── bottom_sheet_profile.xml (new)
    │   ├── item_notification.xml (new)
    │   └── item_profile_menu.xml (new)
    ├── drawable/
    │   ├── bg_bottom_sheet.xml (new)
    │   ├── bg_notification_icon.xml (new)
    │   ├── bg_avatar.xml (new)
    │   ├── bg_notification_badge.xml (new)
    │   ├── ic_bell.xml (new)
    │   ├── ic_clock.xml (new)
    │   ├── ic_close.xml (new)
    │   ├── ic_user.xml (new)
    │   ├── ic_settings.xml (new)
    │   ├── ic_logout.xml (new)
    │   ├── ic_check_circle.xml (new)
    │   └── ic_info.xml (new)
    └── values/
        ├── colors.xml (updated)
        ├── strings.xml (updated)
        └── dimens.xml (new)
```

## Data Models

### Notification.kt
```kotlin
@Parcelize
data class Notification(
    val id: Int,
    val message: String,
    val timestamp: String,
    val type: NotificationType = NotificationType.INFO,
    val isRead: Boolean = false
) : Parcelable

enum class NotificationType {
    INFO,
    REMINDER,
    CONFIRMATION
}
```

### ProfileMenuItem.kt
```kotlin
data class ProfileMenuItem(
    val id: Int,
    val title: String,
    @DrawableRes val icon: Int,
    val isDangerous: Boolean = false,
    val action: ProfileMenuAction
)

enum class ProfileMenuAction {
    VIEW_PROFILE,
    SETTINGS,
    LOGOUT
}
```

## Usage

### Showing Notification Bottom Sheet

```kotlin
private fun showNotificationBottomSheet() {
    val notifications = getNotifications() // Fetch your notifications
    val bottomSheet = NotificationBottomSheet.newInstance(notifications)
    bottomSheet.show(supportFragmentManager, "NotificationBottomSheet")
}
```

### Showing Profile Bottom Sheet

```kotlin
private fun showProfileBottomSheet() {
    val userName = "John Doe" // Get from user session
    val bottomSheet = ProfileBottomSheet.newInstance(userName) { action ->
        handleProfileMenuAction(action)
    }
    bottomSheet.show(supportFragmentManager, "ProfileBottomSheet")
}

private fun handleProfileMenuAction(action: ProfileMenuAction) {
    when (action) {
        ProfileMenuAction.VIEW_PROFILE -> {
            // Navigate to profile screen
        }
        ProfileMenuAction.SETTINGS -> {
            // Navigate to settings screen
        }
        ProfileMenuAction.LOGOUT -> {
            // Handle logout
        }
    }
}
```

## Design Specifications

### Colors
- **Primary Blue**: `#2563EB` (blue_600)
- **Light Blue Background**: `#EFF6FF` (blue_50)
- **Dark Gray Text**: `#111827` (gray_900)
- **Medium Gray Text**: `#374151` (gray_700)
- **Light Gray Text**: `#6B7280` (gray_500)
- **Danger Red**: `#DC2626` (red_600)
- **Border Gray**: `#F3F4F6` (gray_100)

### Spacing
- Extra Small: 4dp
- Small: 8dp
- Medium: 12dp
- Large: 16dp
- Extra Large: 20dp
- 2X Large: 24dp

### Component Sizes
- Icon Small: 16dp
- Icon Medium: 20dp
- Icon Large: 24dp
- Avatar Small: 32dp
- Avatar Medium: 40dp
- List Item Height: 56dp
- Corner Radius: 16dp (top corners of bottom sheets)

## Key Features

### RecyclerView with DiffUtil
Both adapters use `ListAdapter` with `DiffUtil` for efficient list updates:
- Smooth animations when list changes
- Only updates changed items
- Better performance

### ViewBinding
All layouts use ViewBinding for type-safe view access:
- No more `findViewById()`
- Null safety
- Compile-time verification

### Material Design 3
- Material Bottom Sheet Dialog Fragment
- Material Toolbar
- Ripple effects on clickable items
- Proper elevation and shadows

### Lifecycle Aware
- Proper binding cleanup in `onDestroyView()`
- No memory leaks
- Follows Android best practices

## Customization

### Adding More Notification Types

1. Add new type to `NotificationType` enum:
```kotlin
enum class NotificationType {
    INFO,
    REMINDER,
    CONFIRMATION,
    ALERT  // New type
}
```

2. Add corresponding icon in `NotificationAdapter`:
```kotlin
val iconRes = when (notification.type) {
    NotificationType.REMINDER -> R.drawable.ic_clock
    NotificationType.CONFIRMATION -> R.drawable.ic_check_circle
    NotificationType.ALERT -> R.drawable.ic_alert  // New icon
    else -> R.drawable.ic_info
}
```

### Adding More Profile Menu Items

Update `getMenuItems()` in `ProfileBottomSheet.kt`:
```kotlin
private fun getMenuItems(): List<ProfileMenuItem> {
    return listOf(
        ProfileMenuItem(
            id = 1,
            title = "View Profile",
            icon = R.drawable.ic_user,
            action = ProfileMenuAction.VIEW_PROFILE
        ),
        // Add more items here
    )
}
```

## Build Configuration

### Required Gradle Settings

In `app/build.gradle.kts`:

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
    id("kotlin-parcelize")  // For @Parcelize annotation
}

android {
    buildFeatures {
        viewBinding = true  // Enable ViewBinding
    }
}
```

### Dependencies
All required dependencies are already in the project:
- `androidx.appcompat` - AppCompatActivity
- `material` - Material Components (Bottom Sheet, Toolbar)
- `androidx.constraintlayout` - ConstraintLayout
- `androidx.recyclerview` - RecyclerView (part of material)

## Testing

### Manual Testing Checklist

- [ ] Click notification bell icon - bottom sheet appears
- [ ] Click profile icon - bottom sheet appears
- [ ] Notification badge shows when notifications exist
- [ ] Notification badge hides when no notifications
- [ ] Click outside bottom sheet - it dismisses
- [ ] Click X button - bottom sheet dismisses
- [ ] Scroll long notification list - works smoothly
- [ ] Click notification item - bottom sheet dismisses
- [ ] Click "View all notifications" - bottom sheet dismisses
- [ ] Click profile menu items - correct action triggers
- [ ] Logout item shows in red color
- [ ] Rotate device - state preserved correctly

## Next Steps / Future Enhancements

1. **Data Integration**
   - Connect to actual notification API/repository
   - Fetch user data from preferences/session
   - Mark notifications as read

2. **Navigation**
   - Implement actual navigation to profile screen
   - Implement actual navigation to settings screen
   - Implement actual navigation to full notifications screen

3. **Logout Flow**
   - Show confirmation dialog before logout
   - Clear user session/preferences
   - Navigate to login screen

4. **Real-time Updates**
   - WebSocket or Firebase for real-time notifications
   - Update badge count dynamically
   - Show notification toast/snackbar

5. **Persistence**
   - Save notifications to local database (Room)
   - Cache user profile data
   - Remember notification read status

6. **Animations**
   - Add fade-in animation for bottom sheets
   - Add slide-up animation for items
   - Add badge pulse animation

7. **Accessibility**
   - Add content descriptions for all icons
   - Support TalkBack
   - Ensure proper touch target sizes

## Troubleshooting

### Bottom Sheet Not Showing
- Ensure `supportFragmentManager` is used (not `fragmentManager`)
- Check that `show()` is called on UI thread
- Verify bottom sheet tag is unique

### ViewBinding Not Found
- Sync Gradle files
- Clean and rebuild project
- Verify `viewBinding = true` in build.gradle.kts

### Icons Not Displaying
- Check drawable resources are in correct folder
- Verify icon names match in layout and code
- Ensure vector drawables are properly formatted

### Badge Not Showing
- Check `notificationCount > 0`
- Verify visibility is set to `View.VISIBLE`
- Check z-index/elevation settings

## Support
For questions or issues, refer to:
- [Material Design Bottom Sheets](https://material.io/components/sheets-bottom)
- [Android RecyclerView Guide](https://developer.android.com/guide/topics/ui/layout/recyclerview)
- [ViewBinding Documentation](https://developer.android.com/topic/libraries/view-binding)

