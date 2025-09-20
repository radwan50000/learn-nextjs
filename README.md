# Next.js

### Routing in Next Js

> In React you need to install additional react-router package to create routes but next.js is using another thing called ``` File Based Routes ``` this mean the next.js see the folders and the files name and creating routes depend on it

<img
    src='./assets/img1.png'
    alt='file based example'
/>


### Server Side Rendering - SSR

> By default the next.js is using SSR that run the components in the server then return the data of HTML and JS to the user directly of what he need but if you want to use the ``` CSR - Client Side Rendering ``` you can simply put ``` 'use  client' ``` at the top of the main page

<img
    src='./assets/img2.png'
    alt='CSR Use example'
/>

> if i log in console throw the components that it's server side rendering it mustn't be logged but it logged and return at the top SERVER means that logging is coming from the server

<img
    src='./assets/img3.png'
    alt='CSR Use example'
/>

> Note: I can import with two ways in the next project - The ``` @ ``` means the project folder.

<img
    src='./assets/img4.png'
    alt='CSR Use example'
/>


## When to use Server Side Rendering OR Client Side Rendering ?

> There Are table in the next.js Docs clearifying when to choose SSR and CSR


### Routing


# 📖 Auth.js in Next.js (NextAuth v5)
## 🔑 مقدمة

### Auth.js (NextAuth)
> هي مكتبة لإدارة تسجيل الدخول والمصادقة في تطبيقات Next.js 13+. بتدعم مزودين زي GitHub, Google, Facebook … إلخ، وكمان بتشتغل مع credentials أو custom providers.

### الفكرة الأساسية:

> auth() → بتجيب الـ session من السيرفر.

> signIn / signOut → بتتعامل مع تسجيل الدخول والخروج من الكلاينت.

> useSession() → هوك في الكلاينت يجيبلك حالة المستخدم.


🆚 الفرق بين @/app/auth و next-auth/react
Feature	@/app/auth (Server)	next-auth/react (Client)
بيشتغل في	Server Components / Route Handlers	Client Components
أهم الدوال	auth(), signIn, signOut (نسخة سيرفر)	signIn, signOut, useSession
بيعتمد على	cookies(), headers()	API routes + fetch
استخدام شائع	التحقق من session في السيرفر	أزرار login/logout، Hooks للتفاعل مع المستخدم
⚡ أمثلة عملية
### 1️⃣ التحقق من الـ Session في Server Component

```

// app/NavBar.tsx (Server Component)
import { auth } from "@/app/auth";
import NavAuth from "./NavAuth";
import Link from "next/link";

export default async function NavBar() {
  const session = await auth(); // SSR: يتحقق من session

  return (
    <nav className="flex justify-between p-4 bg-white">
      <Link href="/">Home</Link>
      <NavAuth session={session} />
    </nav>
  );
}

```

### 2️⃣ أزرار تسجيل الدخول والخروج (Client Component)

```
// app/NavAuth.tsx (Client Component)
"use client";

import { signIn, signOut } from "next-auth/react";
import Link from "next/link";

export default function NavAuth({ session }: { session: any }) {
  if (session?.user) {
    return (
      <div className="flex gap-2 items-center">
        <Link href={`/user/${session.user.id}`}>
          <span>{session.user.name}</span>
        </Link>
        <button onClick={() => signOut()}>Sign Out</button>
      </div>
    );
  }

  return (
    <div className="flex gap-2 items-center">
      <span>Welcome</span>
      <button onClick={() => signIn("github")}>Sign In</button>
    </div>
  );
}

```

### 3️⃣ استخدام useSession في الكلاينت مباشرة

```

// app/UserInfo.tsx (Client Component)
"use client";

import { useSession } from "next-auth/react";

export default function UserInfo() {
  const { data: session, status } = useSession();

  if (status === "loading") return <p>Loading...</p>;
  if (!session) return <p>You are not logged in</p>;

  return <p>Hello, {session.user?.name} 👋</p>;
}

```
## 💡 Tips & Tricks

```

افصل بين Client و Server Components:

auth() في السيرفر فقط.

signIn/signOut في الكلاينت فقط.

لو محتاج session في كل الصفحات: اعمل wrapper (Server Component) يجيب الـ session ويبعته كـ prop.

Redirect بعد تسجيل الدخول:

signIn("github", { callbackUrl: "/dashboard" });

```

```

حماية Route في السيرفر:

const session = await auth();
if (!session) redirect("/login");


حماية Route في الكلاينت:
استعمل useSession() وتحقق من status === "authenticated".

ديباج: شغل NEXTAUTH_DEBUG=true في .env عشان تعرف تفاصيل أكتر.

```


## 🚀 الخلاصة

```

استعمل @/app/auth لما تكون في Server Component أو API Route.

استعمل next-auth/react لما تتعامل مع buttons, events, hooks في Client Components.

كده بتحصل على أفضل مزيج بين SSR و CSR من غير errors زي:

headers was called outside a request scope

```

### How to Compile Server Side Rendering - With -  Client Side Rendering

> We can simply using form -> the form in Next is sending and runs in the server <br>The Problem is when clicking on the buttons this action is consider as CSR and the action we want to made in the page is SSR here is the problem .<br>Simply we can make form and in it's action we define it's function as SSR and then make the function we want to made this will seperate the button that will live in the page as CSR and the form action is sent to server and runs their as SSR like the following code

```
'use client';

const Home = () => {
  ....

  <form action={async () => {
    'use server';

    await signIn('github'); // This will run in the server
  }}>
    <button type='submit'>
      Login
    </button>
  </form>
}

```

> But Best Practice is to make the button as component and make that component as SSR and the whole page as CSR like this : 


### NavAuth.tsx Page

```

'use client';

import Link from "next/link";
import {signIn, signOut} from "next-auth/react";
import Button from '@/app/components/ButtonOne';
import github from '@/public/github_icon.svg';
import logout from '@/public/signout_icon.svg';
import '@/app/components/NavBar.css';

export default function NavAuth ({session} : {session : any}){
    return (
        <div className='flex items-center gap-4'>
            {
                session && session.user ?
                    <>
                        <Link href={`/user/${session?.user?.id}`}>
                                    <span
                                        className='navbar-text'
                                    >
                                       <span className={'text-pink-700 text-[calc(100%+0.35rem)]'}> Welcome,</span> {session?.user?.name}
                                    </span>
                        </Link>
                        <Button
                            icon={logout}
                            text={'Sign Out'}
                            action={false}
                            />
                    </>
                    :
                    <>
                        <span
                            className='navbar-text'
                        >
                            Let<span className={'text-pink-700'}>'</span>s Sign <span className={'text-pink-700'}>In</span> !!!
                        </span>
                        <Button
                            icon={github}
                            text={'Sign In'}
                            action={true}
                            />
                    </>
            }
        </div>
    )
}

```

### ButtonOne.tsx Page


```

import Image from 'next/image';
import {signIn , signOut} from 'next-auth/react';

const ButtonOne = ({icon , text , action} : {icon: string , text: string , action: boolean }) => {
    return (
        <div
            className='w-fit h-fit
                flex flex-row gap-2 items-center justify-center
                border-t-2 border-l-2 border-r-4 border-b-4
                border-black rounded-md px-4 py-2 cursor-pointer
                transition duration-350 ease-in-out
                bg-white hover:bg-gray-100
            '
            onClick = {() => action ? signIn('github'): signOut() }
        >
            <Image
                src={icon}
                alt={`${text} image`}
                width={30}
                height={30}
                priority
                />
            <p className='inline text-black font-bold text-xl'>
                {text}
            </p>
        </div>
    )
}


export default ButtonOne;

```
