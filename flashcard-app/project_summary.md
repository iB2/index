# next-env.d.ts

```ts
/// <reference types="next" /> /// <reference types="next/image-types/global" /> // NOTE: This file should not be edited // see https://nextjs.org/docs/basic-features/typescript for more information.
```

# next.config.js

```js
/** @type {import('next').NextConfig} */ const nextConfig = { reactStrictMode: true, env: { NEXT_PUBLIC_SUPABASE_URL: process.env.NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY: process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY, }, } module.exports = nextConfig
```

# package.json

```json
{ "name": "flashcard-app", "version": "0.1.0", "private": true, "scripts": { "dev": "next dev", "build": "next build", "start": "next start", "lint": "next lint" }, "dependencies": { "@anthropic-ai/sdk": "^0.4.4", "@google-ai/generativelanguage": "^0.2.1", "@radix-ui/react-label": "^2.1.0", "@radix-ui/react-select": "^1.2.2", "@radix-ui/react-slot": "^1.1.0", "@radix-ui/react-switch": "^1.0.3", "@supabase/auth-helpers-nextjs": "^0.7.2", "@supabase/supabase-js": "^2.26.0", "bufferutil": "^4.0.8", "class-variance-authority": "^0.6.1", "clsx": "^1.2.1", "framer-motion": "^10.12.17", "google-auth-library": "^8.9.0", "lucide-react": "^0.252.0", "next": "13.4.7", "openai": "^4.70.2", "react": "18.2.0", "react-dom": "18.2.0", "tailwind-merge": "^1.14.0", "tailwindcss-animate": "^1.0.7", "utf-8-validate": "^6.0.5" }, "devDependencies": { "@types/node": "20.2.5", "@types/react": "18.2.14", "@types/react-dom": "18.2.6", "autoprefixer": "^10.4.20", "eslint": "8.43.0", "eslint-config-next": "13.4.7", "postcss": "^8.4.47", "tailwindcss": "^3.4.14", "typescript": "5.1.3" } }
```

# postcss.config.js

```js
module.exports = { plugins: { tailwindcss: {}, autoprefixer: {}, }, }
```

# src\app\api\generate-flashcards\route.ts

```ts
import { NextResponse } from 'next/server'; import { createRouteHandlerClient } from '@supabase/auth-helpers-nextjs'; import { cookies } from 'next/headers'; import { generateFlashcards, LLMProvider } from '@/lib/llm-interface'; export async function POST(req: Request) { const supabase = createRouteHandlerClient({ cookies }); try { // Check if user is authenticated const { data: { session } } = await supabase.auth.getSession(); if (!session) { return NextResponse.json({ error: 'Unauthorized' }, { status: 401 }); } const { text, difficulty, provider } = await req.json(); console.log('Received request:', { text, difficulty, provider }); // Log the received data const flashcards = await generateFlashcards(text, difficulty, provider as LLMProvider); console.log('Generated flashcards:', flashcards); // Log the generated flashcards // Save flashcards to database const { data, error } = await supabase.from('flashcard_sets').insert({ user_id: session.user.id, flashcards, difficulty, provider }).select(); if (error) throw error; if (data && data.length > 0) { return NextResponse.json({ flashcards: data[0] }); } else { return NextResponse.json({ error: 'Failed to generate flashcards' }, { status: 500 }); } } catch (error: unknown) { console.error('Error in generate-flashcards API:', error); return NextResponse.json({ error: 'Failed to generate flashcards', details: (error as Error).message }, { status: 500 }); } }
```

# src\app\auth\callback\route.ts

```ts
import { createRouteHandlerClient } from '@supabase/auth-helpers-nextjs' import { cookies } from 'next/headers' import { NextResponse } from 'next/server' export async function GET(request: Request) { const requestUrl = new URL(request.url) const code = requestUrl.searchParams.get('code') if (code) { const supabase = createRouteHandlerClient({ cookies }) await supabase.auth.exchangeCodeForSession(code) } return NextResponse.redirect(requestUrl.origin) }
```

# src\app\globals.css

```css
/* eslint-disable */ /* postcss-preset-env */ @tailwind base; @tailwind components; @tailwind utilities; @layer base { :root { --background: 0 0% 100%; --foreground: 222.2 84% 4.9%; --muted: 210 40% 96.1%; --muted-foreground: 215.4 16.3% 46.9%; --popover: 0 0% 100%; --popover-foreground: 222.2 84% 4.9%; --card: 0 0% 100%; --card-foreground: 222.2 84% 4.9%; --border: 214.3 31.8% 91.4%; --input: 214.3 31.8% 91.4%; --primary: 222.2 47.4% 11.2%; --primary-foreground: 210 40% 98%; --secondary: 210 40% 96.1%; --secondary-foreground: 222.2 47.4% 11.2%; --accent: 210 40% 96.1%; --accent-foreground: 222.2 47.4% 11.2%; --destructive: 0 84.2% 60.2%; --destructive-foreground: 210 40% 98%; --ring: 215 20.2% 65.1%; --radius: 0.5rem; } .dark { --background: 222.2 84% 4.9%; --foreground: 210 40% 98%; --muted: 217.2 32.6% 17.5%; --muted-foreground: 215 20.2% 65.1%; --popover: 222.2 84% 4.9%; --popover-foreground: 210 40% 98%; --card: 222.2 84% 4.9%; --card-foreground: 210 40% 98%; --border: 217.2 32.6% 17.5%; --input: 217.2 32.6% 17.5%; --primary: 210 40% 98%; --primary-foreground: 222.2 47.4% 11.2%; --secondary: 217.2 32.6% 17.5%; --secondary-foreground: 210 40% 98%; --accent: 217.2 32.6% 17.5%; --accent-foreground: 210 40% 98%; --destructive: 0 62.8% 30.6%; --destructive-foreground: 0 85.7% 97.3%; --ring: 217.2 32.6% 17.5%; } } @layer base { * { @apply border-border; } body { @apply bg-background text-foreground; } }
```

# src\app\layout.tsx

```tsx
import './globals.css' import { Inter } from 'next/font/google' const inter = Inter({ subsets: ['latin'] }) export const metadata = { title: 'Flashcard App', description: 'Learn with AI-generated flashcards', } export default function RootLayout({ children, }: { children: React.ReactNode }) { return ( <html lang="en"> <body className={inter.className}>{children}</body> </html> ) }
```

# src\app\login\page.tsx

```tsx
'use client' import { useState } from 'react' import { useRouter } from 'next/navigation' import { createClientComponentClient } from '@supabase/auth-helpers-nextjs' import { Button } from "@/components/ui/button" import { Input } from "@/components/ui/input" import { Label } from "@/components/ui/label" export default function Login() { const [email, setEmail] = useState('') const [password, setPassword] = useState('') const [error, setError] = useState<string | null>(null) const router = useRouter() const supabase = createClientComponentClient() const handleSignIn = async (e: React.FormEvent) => { e.preventDefault() setError(null) const { error } = await supabase.auth.signInWithPassword({ email, password }) if (error) { setError(error.message) } else { router.push('/') } } return ( <div className="flex justify-center items-center min-h-screen bg-gray-100"> <form onSubmit={handleSignIn} className="p-8 bg-white rounded shadow-md w-96"> <h1 className="text-2xl mb-4">Sign In</h1> <div className="mb-4"> <Label htmlFor="email">Email</Label> <Input id="email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} required /> </div> <div className="mb-4"> <Label htmlFor="password">Password</Label> <Input id="password" type="password" value={password} onChange={(e) => setPassword(e.target.value)} required /> </div> {error && <p className="text-red-500 mb-4">{error}</p>} <Button type="submit" className="w-full">Sign In</Button> </form> </div> ) }
```

# src\app\page.tsx

```tsx
import { createServerComponentClient } from '@supabase/auth-helpers-nextjs' import { cookies } from 'next/headers' import { redirect } from 'next/navigation' import FlashcardApp from '@/components/FlashcardApp' export default async function Home() { const supabase = createServerComponentClient({ cookies }) try { const { data: { session } } = await supabase.auth.getSession() if (!session) { redirect('/login') } return <FlashcardApp user={session.user} /> } catch (error) { console.error('Error in Home component:', error) return <div>An error occurred. Please try again later.</div> } }
```

# src\app\reset-password\page.tsx

```tsx
'use client' import { useState } from 'react' import { createClientComponentClient } from '@supabase/auth-helpers-nextjs' import { Button } from '@/components/ui/button' import { Input } from '@/components/ui/input' import { Label } from '@/components/ui/label' export default function ResetPassword() { const [email, setEmail] = useState('') const [isLoading, setIsLoading] = useState(false) const [message, setMessage] = useState<string | null>(null) const supabase = createClientComponentClient() const handleResetPassword = async () => { setIsLoading(true) setMessage(null) const { error } = await supabase.auth.resetPasswordForEmail(email, { redirectTo: `${location.origin}/update-password`, }) setIsLoading(false) if (error) { setMessage(error.message) } else { setMessage('Check your email for the password reset link.') } } return ( <div className="flex flex-col items-center justify-center min-h-screen py-2"> <div className="flex flex-col space-y-4 w-full max-w-md"> <Label htmlFor="email">Email</Label> <Input id="email" type="email" value={email} onChange={(e) => setEmail(e.target.value)} /> <Button onClick={handleResetPassword} disabled={isLoading}> Reset Password </Button> {message && <p className="text-center">{message}</p>} </div> </div> ) }
```

# src\components\FlashcardApp.tsx

```tsx
'use client' import { useState } from "react" import { User } from '@supabase/supabase-js' import { Button } from "@/components/ui/button" import { Input } from "@/components/ui/input" import { Label } from "@/components/ui/label" import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "@/components/ui/card" import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select" import { Flashcard, LLMProvider } from "@/lib/llm-interface" interface FlashcardAppProps { user: User } export default function FlashcardApp({ user }: FlashcardAppProps) { const [text, setText] = useState("") const [flashcards, setFlashcards] = useState<Flashcard[]>([]) const [currentCardIndex, setCurrentCardIndex] = useState(0) const [difficulty, setDifficulty] = useState("medium") const [provider, setProvider] = useState<LLMProvider>("openai") const [showAnswer, setShowAnswer] = useState(false) const [isLoading, setIsLoading] = useState(false) const generateFlashcards = async () => { setIsLoading(true) try { const response = await fetch('/api/generate-flashcards', { method: 'POST', headers: { 'Content-Type': 'application/json', }, body: JSON.stringify({ text, difficulty, provider }), }) if (!response.ok) { throw new Error('Failed to generate flashcards') } const data = await response.json() setFlashcards(data.flashcards) setCurrentCardIndex(0) setShowAnswer(false) } catch (error) { console.error('Error generating flashcards:', error) // Handle error (e.g., show an error message to the user) } finally { setIsLoading(false) } } const handleSwipe = (direction: "left" | "right") => { console.log("Swiped", direction) setCurrentCardIndex((prevIndex) => Math.min(prevIndex + 1, flashcards.length - 1) ) setShowAnswer(false) } return ( <div className="container mx-auto px-4 py-8"> <h1 className="text-4xl font-bold mb-8 text-center">Flashcard App</h1> <div className="grid gap-8 md:grid-cols-2"> <Card> <CardHeader> <CardTitle>Generate Flashcards</CardTitle> <CardDescription>Enter text and choose settings to generate flashcards</CardDescription> </CardHeader> <CardContent className="space-y-4"> <div className="space-y-2"> <Label htmlFor="text">Enter text:</Label> <Input id="text" value={text} onChange={(e) => setText(e.target.value)} /> </div> <div className="space-y-2"> <Label htmlFor="difficulty">Difficulty:</Label> <Select onValueChange={setDifficulty} value={difficulty}> <SelectTrigger> <SelectValue placeholder="Select difficulty" /> </SelectTrigger> <SelectContent> <SelectItem value="easy">Easy</SelectItem> <SelectItem value="medium">Medium</SelectItem> <SelectItem value="hard">Hard</SelectItem> </SelectContent> </Select> </div> <div className="space-y-2"> <Label htmlFor="provider">LLM Provider:</Label> <Select onValueChange={(value) => setProvider(value as LLMProvider)} value={provider}> <SelectTrigger> <SelectValue placeholder="Select LLM provider" /> </SelectTrigger> <SelectContent> <SelectItem value="openai">OpenAI</SelectItem> <SelectItem value="anthropic">Anthropic</SelectItem> <SelectItem value="google">Google (Gemini)</SelectItem> </SelectContent> </Select> </div> </CardContent> <CardFooter> <Button onClick={generateFlashcards} disabled={isLoading} className="w-full"> {isLoading ? 'Generating...' : 'Generate Flashcards'} </Button> </CardFooter> </Card> {flashcards.length > 0 && ( <Card> <CardHeader> <CardTitle>Flashcard {currentCardIndex + 1}</CardTitle> <CardDescription>{flashcards.length} cards total</CardDescription> </CardHeader> <CardContent> <div className="text-center text-2xl mb-4 cursor-pointer" onClick={() => setShowAnswer(!showAnswer)} > {showAnswer ? "Answer:" : "Question:"} <p className="mt-2"> {showAnswer ? flashcards[currentCardIndex].answer : flashcards[currentCardIndex].question} </p> </div> </CardContent> <CardFooter className="flex justify-between"> <Button onClick={() => handleSwipe("left")} variant="outline">Incorrect</Button> <Button onClick={() => handleSwipe("right")}>Correct</Button> </CardFooter> </Card> )} </div> </div> ) }
```

# src\components\ui\button.tsx

```tsx
import * as React from "react" import { Slot } from "@radix-ui/react-slot" import { cva, type VariantProps } from "class-variance-authority" import { cn } from "@/lib/utils" const buttonVariants = cva( "inline-flex items-center justify-center rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50", { variants: { variant: { default: "bg-primary text-primary-foreground hover:bg-primary/90", destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90", outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground", secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80", ghost: "hover:bg-accent hover:text-accent-foreground", link: "text-primary underline-offset-4 hover:underline", }, size: { default: "h-10 px-4 py-2", sm: "h-9 rounded-md px-3", lg: "h-11 rounded-md px-8", icon: "h-10 w-10", }, }, defaultVariants: { variant: "default", size: "default", }, } ) export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>, VariantProps<typeof buttonVariants> { asChild?: boolean } const Button = React.forwardRef<HTMLButtonElement, ButtonProps>( ({ className, variant, size, asChild = false, ...props }, ref) => { const Comp = asChild ? Slot : "button" return ( <Comp className={cn(buttonVariants({ variant, size, className }))} ref={ref} {...props} /> ) } ) Button.displayName = "Button" export { Button, buttonVariants }
```

# src\components\ui\card.tsx

```tsx
import * as React from "react" import { cn } from "@/lib/utils" const Card = React.forwardRef< HTMLDivElement, React.HTMLAttributes<HTMLDivElement> >(({ className, ...props }, ref) => ( <div ref={ref} className={cn( "rounded-lg border bg-card text-card-foreground shadow-sm", className )} {...props} /> )) Card.displayName = "Card" const CardHeader = React.forwardRef< HTMLDivElement, React.HTMLAttributes<HTMLDivElement> >(({ className, ...props }, ref) => ( <div ref={ref} className={cn("flex flex-col space-y-1.5 p-6", className)} {...props} /> )) CardHeader.displayName = "CardHeader" const CardTitle = React.forwardRef< HTMLParagraphElement, React.HTMLAttributes<HTMLHeadingElement> >(({ className, ...props }, ref) => ( <h3 ref={ref} className={cn( "text-2xl font-semibold leading-none tracking-tight", className )} {...props} /> )) CardTitle.displayName = "CardTitle" const CardDescription = React.forwardRef< HTMLParagraphElement, React.HTMLAttributes<HTMLParagraphElement> >(({ className, ...props }, ref) => ( <p ref={ref} className={cn("text-sm text-muted-foreground", className)} {...props} /> )) CardDescription.displayName = "CardDescription" const CardContent = React.forwardRef< HTMLDivElement, React.HTMLAttributes<HTMLDivElement> >(({ className, ...props }, ref) => ( <div ref={ref} className={cn("p-6 pt-0", className)} {...props} /> )) CardContent.displayName = "CardContent" const CardFooter = React.forwardRef< HTMLDivElement, React.HTMLAttributes<HTMLDivElement> >(({ className, ...props }, ref) => ( <div ref={ref} className={cn("flex items-center p-6 pt-0", className)} {...props} /> )) CardFooter.displayName = "CardFooter" export { Card, CardHeader, CardFooter, CardTitle, CardDescription, CardContent }
```

# src\components\ui\input.tsx

```tsx
import * as React from "react" import { cn } from "@/lib/utils" export interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {} const Input = React.forwardRef<HTMLInputElement, InputProps>( ({ className, type, ...props }, ref) => { return ( <input type={type} className={cn( "flex h-10 w-full rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background file:border-0 file:bg-transparent file:text-sm file:font-medium placeholder:text-muted-foreground focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50", className )} ref={ref} {...props} /> ) } ) Input.displayName = "Input" export { Input }
```

# src\components\ui\label.tsx

```tsx
"use client" import * as React from "react" import * as LabelPrimitive from "@radix-ui/react-label" import { cva, type VariantProps } from "class-variance-authority" import { cn } from "@/lib/utils" const labelVariants = cva( "text-sm font-medium leading-none peer-disabled:cursor-not-allowed peer-disabled:opacity-70" ) const Label = React.forwardRef< React.ElementRef<typeof LabelPrimitive.Root>, React.ComponentPropsWithoutRef<typeof LabelPrimitive.Root> & VariantProps<typeof labelVariants> >(({ className, ...props }, ref) => ( <LabelPrimitive.Root ref={ref} className={cn(labelVariants(), className)} {...props} /> )) Label.displayName = LabelPrimitive.Root.displayName export { Label }
```

# src\components\ui\select.tsx

```tsx
"use client" import * as React from "react" import * as SelectPrimitive from "@radix-ui/react-select" import { Check, ChevronDown } from "lucide-react" import { cn } from "@/lib/utils" const Select = SelectPrimitive.Root const SelectGroup = SelectPrimitive.Group const SelectValue = SelectPrimitive.Value const SelectTrigger = React.forwardRef< React.ElementRef<typeof SelectPrimitive.Trigger>, React.ComponentPropsWithoutRef<typeof SelectPrimitive.Trigger> >(({ className, children, ...props }, ref) => ( <SelectPrimitive.Trigger ref={ref} className={cn( "flex h-10 w-full items-center justify-between rounded-md border border-input bg-background px-3 py-2 text-sm ring-offset-background placeholder:text-muted-foreground focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-50", className )} {...props} > {children} <SelectPrimitive.Icon asChild> <ChevronDown className="h-4 w-4 opacity-50" /> </SelectPrimitive.Icon> </SelectPrimitive.Trigger> )) SelectTrigger.displayName = SelectPrimitive.Trigger.displayName const SelectContent = React.forwardRef< React.ElementRef<typeof SelectPrimitive.Content>, React.ComponentPropsWithoutRef<typeof SelectPrimitive.Content> >(({ className, children, position = "popper", ...props }, ref) => ( <SelectPrimitive.Portal> <SelectPrimitive.Content ref={ref} className={cn( "relative z-50 min-w-[8rem] overflow-hidden rounded-md border bg-popover text-popover-foreground shadow-md data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95 data-[side=bottom]:slide-in-from-top-2 data-[side=left]:slide-in-from-right-2 data-[side=right]:slide-in-from-left-2 data-[side=top]:slide-in-from-bottom-2", position === "popper" && "data-[side=bottom]:translate-y-1 data-[side=left]:-translate-x-1 data-[side=right]:translate-x-1 data-[side=top]:-translate-y-1", className )} position={position} {...props} > <SelectPrimitive.Viewport className={cn( "p-1", position === "popper" && "h-[var(--radix-select-trigger-height)] w-full min-w-[var(--radix-select-trigger-width)]" )} > {children} </SelectPrimitive.Viewport> </SelectPrimitive.Content> </SelectPrimitive.Portal> )) SelectContent.displayName = SelectPrimitive.Content.displayName const SelectLabel = React.forwardRef< React.ElementRef<typeof SelectPrimitive.Label>, React.ComponentPropsWithoutRef<typeof SelectPrimitive.Label> >(({ className, ...props }, ref) => ( <SelectPrimitive.Label ref={ref} className={cn("py-1.5 pl-8 pr-2 text-sm font-semibold", className)} {...props} /> )) SelectLabel.displayName = SelectPrimitive.Label.displayName const SelectItem = React.forwardRef< React.ElementRef<typeof SelectPrimitive.Item>, React.ComponentPropsWithoutRef<typeof SelectPrimitive.Item> >(({ className, children, ...props }, ref) => ( <SelectPrimitive.Item ref={ref} className={cn( "relative flex w-full cursor-default select-none items-center rounded-sm py-1.5 pl-8 pr-2 text-sm outline-none focus:bg-accent focus:text-accent-foreground data-[disabled]:pointer-events-none data-[disabled]:opacity-50", className )} {...props} > <span className="absolute left-2 flex h-3.5 w-3.5 items-center justify-center"> <SelectPrimitive.ItemIndicator> <Check className="h-4 w-4" /> </SelectPrimitive.ItemIndicator> </span> <SelectPrimitive.ItemText>{children}</SelectPrimitive.ItemText> </SelectPrimitive.Item> )) SelectItem.displayName = SelectPrimitive.Item.displayName const SelectSeparator = React.forwardRef< React.ElementRef<typeof SelectPrimitive.Separator>, React.ComponentPropsWithoutRef<typeof SelectPrimitive.Separator> >(({ className, ...props }, ref) => ( <SelectPrimitive.Separator ref={ref} className={cn("-mx-1 my-1 h-px bg-muted", className)} {...props} /> )) SelectSeparator.displayName = SelectPrimitive.Separator.displayName export { Select, SelectGroup, SelectValue, SelectTrigger, SelectContent, SelectLabel, SelectItem, SelectSeparator, }
```

# src\lib\llm-interface.ts

```ts
import OpenAI from 'openai'; export interface Flashcard { question: string; answer: string; } export type LLMProvider = 'openai'; const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY, }); export async function generateFlashcards( text: string, difficulty: string, provider: LLMProvider, numCards: number = 5 ): Promise<Flashcard[]> { const prompt = `Create ${numCards} flashcards with questions and answers based on the following text. The difficulty level is ${difficulty}. Text: ${text}`; return generateOpenAIFlashcards(prompt); } async function generateOpenAIFlashcards(prompt: string): Promise<Flashcard[]> { const response = await openai.chat.completions.create({ model: "gpt-3.5-turbo", messages: [{ role: "user", content: prompt }], temperature: 0.7, }); const flashcardsText = response.choices[0].message?.content; if (!flashcardsText) { throw new Error('No flashcards generated'); } return parseFlashcards(flashcardsText); } function parseFlashcards(text: string): Flashcard[] { const flashcards = text.split('\n\n').map(card => { const [question, answer] = card.split('\nAnswer: '); return { question: question.replace('Question: ', '').trim(), answer: answer.trim(), }; }); return flashcards.filter(card => card.question && card.answer); }
```

# src\lib\openai.ts

```ts
import { Configuration, OpenAIApi } from 'openai'; const configuration = new Configuration({ apiKey: process.env.OPENAI_API_KEY, }); const openai = new OpenAIApi(configuration); export async function generateFlashcards(text: string, difficulty: string, numCards: number = 5) { try { const response = await openai.createChatCompletion({ model: "gpt-3.5-turbo", messages: [ { role: "system", content: `You are a helpful AI assistant that creates flashcards based on given text. Create ${numCards} flashcards with questions and answers. The difficulty level is ${difficulty}.` }, { role: "user", content: `Create flashcards based on this text: ${text}` } ], temperature: 0.7, }); const flashcardsText = response.data.choices[0].message?.content; if (!flashcardsText) { throw new Error('No flashcards generated'); } // Parse the flashcards from the generated text const flashcards = flashcardsText.split('\n\n').map(card => { const [question, answer] = card.split('\nAnswer: '); return { question: question.replace('Question: ', ''), answer }; }); return flashcards; } catch (error) { console.error('Error generating flashcards:', error); throw error; } }
```

# src\lib\supabase-client.ts

```ts
import { createClient } from '@supabase/supabase-js' const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY if (!supabaseUrl || !supabaseAnonKey) { throw new Error('Missing Supabase environment variables') } export const supabase = createClient(supabaseUrl, supabaseAnonKey)
```

# src\lib\supabase.ts

```ts
import { createClient } from '@supabase/supabase-js' const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL! const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY! export const createSupabaseClient = async () => { const { createClient } = await import('@supabase/supabase-js') return createClient(supabaseUrl, supabaseAnonKey) }
```

# src\lib\utils.ts

```ts
import { type ClassValue, clsx } from "clsx" import { twMerge } from "tailwind-merge" export function cn(...inputs: ClassValue[]) { return twMerge(clsx(inputs)) }
```

# supabase_improved_setup.sql

```sql
-- Create users table CREATE TABLE users ( id UUID DEFAULT auth.uid() PRIMARY KEY, email TEXT UNIQUE NOT NULL, created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP ); -- Create flashcard_sets table (updated) CREATE TABLE flashcard_sets ( id UUID DEFAULT uuid_generate_v4() PRIMARY KEY, user_id UUID REFERENCES users(id) ON DELETE CASCADE, title TEXT NOT NULL, flashcards JSONB, difficulty TEXT, provider TEXT, created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP ); -- Create user_progress table (updated) CREATE TABLE user_progress ( id UUID DEFAULT uuid_generate_v4() PRIMARY KEY, user_id UUID REFERENCES users(id) ON DELETE CASCADE, flashcard_set_id UUID REFERENCES flashcard_sets(id) ON DELETE CASCADE, flashcard_index INTEGER, correct BOOLEAN, created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP ); -- Create a trigger to automatically insert a new user into the users table CREATE OR REPLACE FUNCTION public.handle_new_user() RETURNS TRIGGER AS $$ BEGIN INSERT INTO public.users (id, email) VALUES (new.id, new.email); RETURN NEW; END; $$ LANGUAGE plpgsql SECURITY DEFINER; CREATE TRIGGER on_auth_user_created AFTER INSERT ON auth.users FOR EACH ROW EXECUTE PROCEDURE public.handle_new_user(); -- Create a function to update the updated_at timestamp CREATE OR REPLACE FUNCTION update_modified_column() RETURNS TRIGGER AS $$ BEGIN NEW.updated_at = now(); RETURN NEW; END; $$ LANGUAGE plpgsql; -- Create triggers to automatically update the updated_at timestamp CREATE TRIGGER update_users_modtime BEFORE UPDATE ON users FOR EACH ROW EXECUTE FUNCTION update_modified_column(); CREATE TRIGGER update_flashcard_sets_modtime BEFORE UPDATE ON flashcard_sets FOR EACH ROW EXECUTE FUNCTION update_modified_column();
```

# supabase_setup.sql

```sql
-- Create flashcard_sets table CREATE TABLE flashcard_sets ( id UUID DEFAULT uuid_generate_v4() PRIMARY KEY, user_id UUID REFERENCES auth.users(id), flashcards JSONB, difficulty TEXT, provider TEXT, created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP ); -- Create user_progress table CREATE TABLE user_progress ( id UUID DEFAULT uuid_generate_v4() PRIMARY KEY, user_id UUID REFERENCES auth.users(id), flashcard_id UUID, correct BOOLEAN, created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP );
```

# tailwind.config.js

```js
/** @type {import('tailwindcss').Config} */ module.exports = { darkMode: ["class"], content: [ './pages/**/*.{ts,tsx}', './components/**/*.{ts,tsx}', './app/**/*.{ts,tsx}', './src/**/*.{ts,tsx}', ], theme: { container: { center: true, padding: "2rem", screens: { "2xl": "1400px", }, }, extend: { colors: { border: "hsl(var(--border))", input: "hsl(var(--input))", ring: "hsl(var(--ring))", background: "hsl(var(--background))", foreground: "hsl(var(--foreground))", primary: { DEFAULT: "hsl(var(--primary))", foreground: "hsl(var(--primary-foreground))", }, secondary: { DEFAULT: "hsl(var(--secondary))", foreground: "hsl(var(--secondary-foreground))", }, destructive: { DEFAULT: "hsl(var(--destructive))", foreground: "hsl(var(--destructive-foreground))", }, muted: { DEFAULT: "hsl(var(--muted))", foreground: "hsl(var(--muted-foreground))", }, accent: { DEFAULT: "hsl(var(--accent))", foreground: "hsl(var(--accent-foreground))", }, popover: { DEFAULT: "hsl(var(--popover))", foreground: "hsl(var(--popover-foreground))", }, card: { DEFAULT: "hsl(var(--card))", foreground: "hsl(var(--card-foreground))", }, }, borderRadius: { lg: "var(--radius)", md: "calc(var(--radius) - 2px)", sm: "calc(var(--radius) - 4px)", }, keyframes: { "accordion-down": { from: { height: 0 }, to: { height: "var(--radix-accordion-content-height)" }, }, "accordion-up": { from: { height: "var(--radix-accordion-content-height)" }, to: { height: 0 }, }, }, animation: { "accordion-down": "accordion-down 0.2s ease-out", "accordion-up": "accordion-up 0.2s ease-out", }, }, }, plugins: [require("tailwindcss-animate")], }
```

# tsconfig.json

```json
{ "compilerOptions": { "target": "es5", "lib": ["dom", "dom.iterable", "esnext"], "allowJs": true, "skipLibCheck": true, "strict": true, "forceConsistentCasingInFileNames": true, "noEmit": true, "esModuleInterop": true, "module": "esnext", "moduleResolution": "node", "resolveJsonModule": true, "isolatedModules": true, "jsx": "preserve", "incremental": true, "plugins": [ { "name": "next" } ], "paths": { "@/*": ["./src/*"] } }, "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"], "exclude": ["node_modules"] }
```

