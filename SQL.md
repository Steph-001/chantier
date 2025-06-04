
-- Enable UUID extension if not already enabled
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create flashcard_progress table
CREATE TABLE IF NOT EXISTS public.flashcard_progress (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
    set_id TEXT NOT NULL,
    set_title TEXT,
    card_data JSONB NOT NULL DEFAULT '{}'::jsonb,
    stats JSONB NOT NULL DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE(user_id, set_id)
);

-- Add RLS (Row Level Security) policies
ALTER TABLE public.flashcard_progress ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only view their own progress
CREATE POLICY "Users can view own progress" 
ON public.flashcard_progress 
FOR SELECT 
USING (auth.uid() = user_id);

-- Policy: Users can insert their own progress
CREATE POLICY "Users can insert own progress" 
ON public.flashcard_progress 
FOR INSERT 
WITH CHECK (auth.uid() = user_id);

-- Policy: Users can update their own progress
CREATE POLICY "Users can update own progress" 
ON public.flashcard_progress 
FOR UPDATE 
USING (auth.uid() = user_id);

-- Create index for faster queries
CREATE INDEX IF NOT EXISTS flashcard_progress_user_set_idx 
ON public.flashcard_progress (user_id, set_id);

