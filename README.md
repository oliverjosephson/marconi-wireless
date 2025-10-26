# Marconi Wireless Telegraph

A vintage-styled wireless telegraph application with real-time messaging powered by Supabase.

## Features

- 🔐 **Magic Link Authentication** - Secure email-based login
- 📡 **Real-time Messaging** - Messages broadcast instantly to all connected users
- 🎵 **Morse Code Audio** - Hear incoming messages in authentic Morse code beeps
- 📻 **Vintage UI** - Beautiful retro-styled interface inspired by early wireless telegraphy

## Quick Start

### Running the Application

Simply open `index.html` in a web browser or serve it with a local web server:

```bash
# Using Python
python -m http.server 8000

# Using Node.js (http-server)
npx http-server

# Using PHP
php -S localhost:8000
```

Then navigate to `http://localhost:8000` in your browser.

### Using the App

1. Enter your email address on the login screen
2. Check your email for the magic link
3. Click the link to authenticate
4. Start sending messages to all connected users!

## File Structure

```
marconi-wireless/
├── index.html      # Main HTML structure
├── styles.css      # All styling (separated from HTML)
├── app.js          # Application logic and Supabase integration
└── README.md       # This file
```

## How It Works

### Authentication Flow

1. User enters their email address
2. Magic link is sent to their email
3. User clicks the link to authenticate
4. App displays the messaging interface

### Messaging Flow

1. User types a message and clicks "SEND TRANSMISSION"
2. Message is converted to Morse code
3. Message is broadcast to all connected users in real-time
4. Morse code audio plays automatically for incoming messages

## Credits

Built with:
- [Supabase](https://supabase.com) - Backend and real-time infrastructure
- Vanilla JavaScript - No frameworks needed!
- Web Audio API - For authentic Morse code beeps

---

## Developer Setup

### Supabase Configuration

You need to configure your Supabase credentials in `app.js`:

1. Open `app.js`
2. Replace `YOUR_SUPABASE_ANON_KEY` with your actual Supabase anonymous key
3. Verify the `SUPABASE_URL` matches your project URL

### Supabase Database Setup

Your Supabase project should have a `morse_messages` table with the following schema:

```sql
CREATE TABLE morse_messages (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  room TEXT NOT NULL DEFAULT 'global',
  username TEXT,
  payload JSONB NOT NULL,
  user_id UUID REFERENCES auth.users(id),
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE morse_messages ENABLE ROW LEVEL SECURITY;

-- Allow authenticated users to read all messages
CREATE POLICY "Allow authenticated users to read messages"
ON morse_messages FOR SELECT
TO authenticated
USING (true);

-- Allow authenticated users to insert messages
CREATE POLICY "Allow authenticated users to insert messages"
ON morse_messages FOR INSERT
TO authenticated
WITH CHECK (true);

-- Enable realtime
ALTER PUBLICATION supabase_realtime ADD TABLE morse_messages;
```

### Supabase Authentication Setup

1. In your Supabase dashboard, go to **Authentication > Providers**
2. Enable **Email** provider
3. Configure email templates if desired
4. Set up your site URL in **Authentication > URL Configuration**

### Message Payload Structure

Messages are stored in the `payload` JSONB column with this structure:

```json
{
  "username": "user@example.com",
  "message": "Hello World",
  "morse": ".... . .-.. .-.. --- / .-- --- .-. .-.. -..",
  "encoding": "morse",
  "sent_at": "2025-10-25T13:20:47.000Z"
}
```

## Troubleshooting

### Messages not appearing in real-time

**Most Common Fix:** Make sure you're testing with TWO separate browsers/users! Realtime only broadcasts to OTHER users, not the sender.

Other checks:
- Open browser console (F12) and look for `"Channel status: SUBSCRIBED"`
- Check that realtime is enabled for the `morse_messages` table
- Verify your RLS policies allow authenticated users to read messages
- Look for any connection errors in the console

### No Morse code sound playing

- Click anywhere on the page first (browsers require user interaction before playing audio)
- Check that your browser audio isn't muted
- Try a different browser (some have stricter autoplay policies)
- Look for audio errors in the browser console

### Magic link not sending

- Verify email provider is enabled in Supabase
- Check your email spam folder
- Ensure SMTP is properly configured in Supabase

### Authentication errors

- Make sure your Supabase URL and anon key are correct
- Check that the site URL is configured in Supabase settings
- Verify RLS policies are set up correctly

## Teaching Notes

This project demonstrates several important web development concepts:

1. **Real-time Communication** - Messages broadcast instantly using WebSockets
2. **Authentication** - Passwordless magic link login
3. **Database Security** - Row Level Security (RLS) policies control access
4. **Web Audio API** - Generating sounds programmatically
5. **Async/Await** - Modern JavaScript for handling asynchronous operations
6. **Browser APIs** - Working with browser limitations (autoplay policies)
