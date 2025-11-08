# 📱 Messages & Records Tab Implementation Guide

## ✅ Completed Components

### 📨 Messages Tab - Data Models
1. **Message.kt** - Message data model with SenderType enum and MessageAttachment
2. **Conversation.kt** - Conversation data model for inbox
3. **Notification.kt** - NotificationItem with NotificationType enum

### 📁 Records Tab - Data Models
1. **PatientProfile.kt** - Patient personal information
2. **DentalRecord.kt** - Dental history with RecordStatus and TreatmentType enums
3. **DentalResource.kt** - Medical resources with ResourceCategory and ResourceFileType enums

### 🎨 Layouts Created

#### Messages Tab Layouts
- `fragment_messages.xml` - Main container with TabLayout + ViewPager2
- `tab_messages_inbox.xml` - Inbox/Conversations list with search
- `tab_messages_chat.xml` - Chat view with messages, input, and attachment
- `tab_messages_notifications.xml` - Notifications list
- `item_conversation.xml` - Conversation list item with profile, unread badge
- `item_message.xml` - Message bubble (sent/received)
- `item_notification.xml` - Notification item with icon and timestamp

#### Records Tab Layouts
- `fragment_records.xml` - Main container with TabLayout + ViewPager2
- `tab_records_profile.xml` - Patient profile with cards (Personal, Contact, Dentist info)
- `tab_records_history.xml` - Dental history with filter chips
- `tab_records_resources.xml` - Resources with search and category filters
- `item_dental_record.xml` - Dental record card with status chip
- `item_resource.xml` - Resource item with icon, view, and download buttons

### 🎨 Drawable Resources
- `bg_status_indicator.xml` - Green dot for online status
- `bg_send_button.xml` - Blue circular send button
- `bg_icon_container.xml` - Blue rounded container for resource icons
- `ic_back.xml` - Back arrow icon
- `ic_send.xml` - Send message icon
- `ic_attachment.xml` - Attachment clip icon

### 🎨 Styles Added
- `CircleImageView` - Rounded image style for profile pictures

---

## 🔨 Implementation Required

### 1. Fragment Classes (Kotlin)

You need to create these fragment files:

#### Messages Tab Fragments

**`app/src/main/java/com/zeke/brightcaredentalpatient/ui/fragments/messages/MessagesFragment.kt`**
```kotlin
package com.zeke.brightcaredentalpatient.ui.fragments.messages

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment
import androidx.viewpager2.widget.ViewPager2
import com.google.android.material.tabs.TabLayout
import com.google.android.material.tabs.TabLayoutMediator
import com.zeke.brightcaredentalpatient.R
import com.zeke.brightcaredentalpatient.ui.adapters.MessagesPagerAdapter

class MessagesFragment : Fragment() {
    
    private lateinit var tabLayout: TabLayout
    private lateinit var viewPager: ViewPager2
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_messages, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        tabLayout = view.findViewById(R.id.tabLayout)
        viewPager = view.findViewById(R.id.viewPager)
        
        setupViewPager()
    }
    
    private fun setupViewPager() {
        val adapter = MessagesPagerAdapter(this)
        viewPager.adapter = adapter
        
        TabLayoutMediator(tabLayout, viewPager) { tab, position ->
            tab.text = when (position) {
                0 -> "Inbox"
                1 -> "Chat"
                2 -> "Notifications"
                else -> ""
            }
        }.attach()
    }
}
```

**Similarly create:**
- `InboxTabFragment.kt` - For inbox/conversations list
- `ChatTabFragment.kt` - For chat view
- `NotificationsTabFragment.kt` - For notifications list

#### Records Tab Fragments

**`app/src/main/java/com/zeke/brightcaredentalpatient/ui/fragments/records/RecordsFragment.kt`**
```kotlin
package com.zeke.brightcaredentalpatient.ui.fragments.records

import android.os.Bundle
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import androidx.fragment.app.Fragment
import androidx.viewpager2.widget.ViewPager2
import com.google.android.material.tabs.TabLayout
import com.google.android.material.tabs.TabLayoutMediator
import com.zeke.brightcaredentalpatient.R
import com.zeke.brightcaredentalpatient.ui.adapters.RecordsPagerAdapter

class RecordsFragment : Fragment() {
    
    private lateinit var tabLayout: TabLayout
    private lateinit var viewPager: ViewPager2
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_records, container, false)
    }
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        
        tabLayout = view.findViewById(R.id.tabLayout)
        viewPager = view.findViewById(R.id.viewPager)
        
        setupViewPager()
    }
    
    private fun setupViewPager() {
        val adapter = RecordsPagerAdapter(this)
        viewPager.adapter = adapter
        
        TabLayoutMediator(tabLayout, viewPager) { tab, position ->
            tab.text = when (position) {
                0 -> "Profile"
                1 -> "Dental History"
                2 -> "Resources"
                else -> ""
            }
        }.attach()
    }
}
```

**Similarly create:**
- `ProfileTabFragment.kt` - For patient profile
- `DentalHistoryTabFragment.kt` - For dental history list
- `ResourcesTabFragment.kt` - For resources list

---

### 2. RecyclerView Adapters

Create these adapter files:

**`app/src/main/java/com/zeke/brightcaredentalpatient/ui/adapters/ConversationsAdapter.kt`**
```kotlin
package com.zeke.brightcaredentalpatient.ui.adapters

import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.ImageView
import android.widget.TextView
import androidx.recyclerview.widget.DiffUtil
import androidx.recyclerview.widget.ListAdapter
import androidx.recyclerview.widget.RecyclerView
import com.zeke.brightcaredentalpatient.R
import com.zeke.brightcaredentalpatient.data.models.Conversation

class ConversationsAdapter(
    private val onItemClick: (Conversation) -> Unit
) : ListAdapter<Conversation, ConversationsAdapter.ViewHolder>(DiffCallback()) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.item_conversation, parent, false)
        return ViewHolder(view, onItemClick)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(getItem(position))
    }

    class ViewHolder(
        itemView: View,
        private val onItemClick: (Conversation) -> Unit
    ) : RecyclerView.ViewHolder(itemView) {
        
        private val imageProfile: ImageView = itemView.findViewById(R.id.imageProfile)
        private val statusIndicator: View = itemView.findViewById(R.id.statusIndicator)
        private val textDentistName: TextView = itemView.findViewById(R.id.textDentistName)
        private val textLastMessage: TextView = itemView.findViewById(R.id.textLastMessage)
        private val textTime: TextView = itemView.findViewById(R.id.textTime)
        private val badgeUnread: TextView = itemView.findViewById(R.id.badgeUnread)

        fun bind(conversation: Conversation) {
            textDentistName.text = conversation.dentistName
            textLastMessage.text = conversation.getLastMessagePreview()
            textTime.text = conversation.getLastActivityTime()
            
            statusIndicator.visibility = if (conversation.isOnline) View.VISIBLE else View.GONE
            
            if (conversation.unreadCount > 0) {
                badgeUnread.visibility = View.VISIBLE
                badgeUnread.text = conversation.unreadCount.toString()
            } else {
                badgeUnread.visibility = View.GONE
            }
            
            itemView.setOnClickListener { onItemClick(conversation) }
        }
    }

    class DiffCallback : DiffUtil.ItemCallback<Conversation>() {
        override fun areItemsTheSame(oldItem: Conversation, newItem: Conversation) =
            oldItem.id == newItem.id
        override fun areContentsTheSame(oldItem: Conversation, newItem: Conversation) =
            oldItem == newItem
    }
}
```

**Similarly create:**
- `MessagesAdapter.kt` - For chat messages
- `NotificationsAdapter.kt` - For notifications
- `DentalRecordsAdapter.kt` - For dental history
- `ResourcesAdapter.kt` - For dental resources
- `MessagesPagerAdapter.kt` - ViewPager2 adapter for Messages tabs
- `RecordsPagerAdapter.kt` - ViewPager2 adapter for Records tabs

---

### 3. ViewPager2 Fragment Adapters

**`app/src/main/java/com/zeke/brightcaredentalpatient/ui/adapters/MessagesPagerAdapter.kt`**
```kotlin
package com.zeke.brightcaredentalpatient.ui.adapters

import androidx.fragment.app.Fragment
import androidx.viewpager2.adapter.FragmentStateAdapter
import com.zeke.brightcaredentalpatient.ui.fragments.messages.InboxTabFragment
import com.zeke.brightcaredentalpatient.ui.fragments.messages.ChatTabFragment
import com.zeke.brightcaredentalpatient.ui.fragments.messages.NotificationsTabFragment

class MessagesPagerAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {
    
    override fun getItemCount(): Int = 3
    
    override fun createFragment(position: Int): Fragment {
        return when (position) {
            0 -> InboxTabFragment()
            1 -> ChatTabFragment()
            2 -> NotificationsTabFragment()
            else -> throw IllegalArgumentException("Invalid position: $position")
        }
    }
}
```

**Similarly create:**
- `RecordsPagerAdapter.kt` - For Records tabs

---

### 4. Repository/Data Source (Optional but recommended)

Create mock data repositories:

**`app/src/main/java/com/zeke/brightcaredentalpatient/data/repository/MessageRepository.kt`**
```kotlin
package com.zeke.brightcaredentalpatient.data.repository

import com.zeke.brightcaredentalpatient.data.models.*
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow

object MessageRepository {
    
    private val _conversations = MutableStateFlow<List<Conversation>>(getSampleConversations())
    val conversations: StateFlow<List<Conversation>> = _conversations
    
    private val _messages = mutableMapOf<String, MutableList<Message>>()
    
    fun getConversations(): List<Conversation> = _conversations.value
    
    fun getMessages(conversationId: String): List<Message> {
        return _messages.getOrPut(conversationId) {
            getSampleMessages(conversationId).toMutableList()
        }
    }
    
    fun sendMessage(conversationId: String, content: String) {
        val message = Message(
            conversationId = conversationId,
            senderId = "patient_001",
            senderName = "You",
            senderType = SenderType.PATIENT,
            content = content
        )
        _messages.getOrPut(conversationId) { mutableListOf() }.add(message)
    }
    
    private fun getSampleConversations(): List<Conversation> {
        return listOf(
            Conversation(
                id = "conv_001",
                dentistId = "dentist_001",
                dentistName = "Dr. Maria Santos",
                isOnline = true,
                unreadCount = 3,
                lastMessage = Message(
                    conversationId = "conv_001",
                    senderId = "dentist_001",
                    senderName = "Dr. Maria Santos",
                    senderType = SenderType.DENTIST,
                    content = "Your next appointment is confirmed for tomorrow at 10 AM.",
                    timestamp = System.currentTimeMillis() - 7200000 // 2 hours ago
                )
            ),
            // Add more sample conversations...
        )
    }
    
    private fun getSampleMessages(conversationId: String): List<Message> {
        return listOf(
            Message(
                conversationId = conversationId,
                senderId = "patient_001",
                senderName = "You",
                senderType = SenderType.PATIENT,
                content = "Hello, I have a question about my appointment.",
                timestamp = System.currentTimeMillis() - 3600000
            ),
            Message(
                conversationId = conversationId,
                senderId = "dentist_001",
                senderName = "Dr. Maria Santos",
                senderType = SenderType.DENTIST,
                content = "Sure! I'm happy to help. What would you like to know?",
                timestamp = System.currentTimeMillis() - 3000000
            )
        )
    }
}
```

**Similarly create:**
- `RecordsRepository.kt` - For patient profile, dental history, and resources

---

### 5. Integration with MainActivity

Update `MainActivity.kt` bottom navigation to include Messages and Records:

```kotlin
// In MainActivity's setupBottomNavigation()
bottomNav.setOnItemSelectedListener { item ->
    when (item.itemId) {
        R.id.nav_home -> {
            loadFragment(HomeFragment())
            true
        }
        R.id.nav_appointment -> {
            loadFragment(AppointmentFragment())
            true
        }
        R.id.nav_messages -> {
            loadFragment(MessagesFragment())
            true
        }
        R.id.nav_records -> {
            loadFragment(RecordsFragment())
            true
        }
        else -> false
    }
}
```

Update `res/menu/bottom_navigation_menu.xml`:
```xml
<item
    android:id="@+id/nav_messages"
    android:icon="@drawable/ic_email"
    android:title="Messages" />

<item
    android:id="@+id/nav_records"
    android:icon="@drawable/ic_location"
    android:title="Records" />
```

---

## 📋 Feature Breakdown

### 💬 Messages Tab Features
1. **Inbox/Conversations**
   - List all conversations with dentists/staff
   - Search functionality
   - Unread message indicators
   - Online/offline status
   - Last message preview with timestamp

2. **Chat View**
   - Real-time messaging interface
   - Message bubbles (sent/received)
   - Attachment support (images, documents)
   - Typing indicator
   - Send message functionality

3. **Notifications**
   - Appointment reminders
   - Clinic updates
   - Treatment plan updates
   - Payment alerts
   - Message notifications

### 📁 Records Tab Features
1. **Profile**
   - Profile photo
   - Personal information (name, birthdate, age)
   - Contact information (phone, email, address)
   - Assigned dentist
   - Edit profile button

2. **Dental History**
   - List of all treatments
   - Filter by status (Completed, Ongoing, Follow-up)
   - Each record shows:
     - Date
     - Diagnosis
     - Treatment
     - Dentist name
     - Notes
     - Status chip with color coding
   - View details functionality

3. **Resources**
   - Categorized files (X-Rays, Documents, Educational)
   - Search functionality
   - Category filters
   - Each resource shows:
     - Icon based on type
     - Title and description
     - Upload date
     - File size
     - View and Download actions

---

## 🎨 Design Features Implemented
- ✅ Material Design 3 components
- ✅ Gradient headers matching app theme
- ✅ Rounded cards with elevation
- ✅ Empty state screens
- ✅ Search bars with icons
- ✅ Filter chips for status/category
- ✅ Status indicators (online, unread)
- ✅ Professional color scheme (mlue, skyblue, gray scale)
- ✅ Custom fonts (Plus Jakarta Sans, Poppins)
- ✅ Smooth transitions and animations (via ViewPager2)

---

## 🚀 Next Steps

1. Create all fragment Kotlin files listed above
2. Create all adapter Kotlin files listed above
3. Implement repository/data sources for mock data
4. Test each tab individually
5. Add image loading library (Glide or Coil) for profile pictures and attachments
6. Implement actual data persistence (Room Database or Firebase)
7. Add real-time messaging (Firebase or WebSocket)
8. Implement file upload/download functionality
9. Add push notifications for messages
10. Test the complete flow end-to-end

---

## 📝 Notes

- All layouts follow Material Design 3 guidelines
- Uses signature colors from your app (mlue, skyblue)
- All text views use custom fonts
- Empty states included for better UX
- RecyclerViews use DiffUtil for efficient updates
- ViewBinding recommended for all fragments
- Lifecycle-aware components used throughout

---

Tapos na ang lahat ng layouts at data models! ✅

Kailangan mo na lang gawin:
1. Mag-create ng mga Fragment classes
2. Mag-create ng mga Adapter classes
3. I-integrate sa MainActivity
4. I-test! 🎉

