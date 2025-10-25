# Marconi Wireless Telegraph

A vintage-styled wireless telegraph application with real-time messaging powered by Supabase.

## Features

- 🔐 **Magic Link Authentication** - Secure email-based login via Supabase
- 📡 **Real-time Messaging** - Messages broadcast instantly to all connected users
- 🎵 **Morse Code Audio** - Hear incoming messages in authentic Morse code beeps
- 📻 **Vintage UI** - Beautiful retro-styled interface inspired by early wireless telegraphy

## Setup Instructions

### 1. Supabase Configuration

You need to configure your Supabase credentials in `app.js`:

1. Open `app.js`
2. Replace `YOUR_SUPABASE_ANON_KEY` with your actual Supabase anonymous key
3. Verify the `SUPABASE_URL` matches your project URL: `https://afswezwmfjsgupgdcybl.supabase.co`

### 2. Supabase Database Setup

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

### 3. Supabase Authentication Setup

1. In your Supabase dashboard, go to **Authentication > Providers**
2. Enable **Email** provider
3. Configure email templates if desired
4. Set up your site URL in **Authentication > URL Configuration**

### 4. Running the Application

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

## File Structure

```
marconi-wireless/
├── index.html      # Main HTML structure
├── styles.css      # All styling (separated from HTML)
├── app.js          # Application logic and Supabase integration
├── .env.example    # Example environment configuration
└── README.md       # This file
```

## How It Works

### Authentication Flow

1. User enters their email address
2. Supabase sends a magic link to their email
3. User clicks the link to authenticate
4. App displays the messaging interface

### Messaging Flow

1. User types a message and clicks "SEND TRANSMISSION"
2. Message is converted to Morse code
3. Message is inserted into Supabase `morse_messages` table
4. Supabase broadcasts the new message to all subscribed clients
5. All connected users receive the message in real-time
6. Morse code audio plays automatically for incoming messages

## Payload Structure

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

- Check that realtime is enabled for the `morse_messages` table
- Verify your RLS policies allow authenticated users to read messages
- Check browser console for connection errors

### Magic link not sending

- Verify email provider is enabled in Supabase
- Check your email spam folder
- Ensure SMTP is properly configured in Supabase

### Authentication errors

- Make sure your Supabase URL and anon key are correct
- Check that the site URL is configured in Supabase settings
- Verify RLS policies are set up correctly

## Credits

Built with:
- [Supabase](https://supabase.com) - Backend and real-time infrastructure
- Vanilla JavaScript - No frameworks needed!
- Web Audio API - For authentic Morse code beeps
