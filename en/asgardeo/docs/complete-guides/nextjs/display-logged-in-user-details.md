---
template: templates/complete-guide.html
heading: Display logged-in user details
read_time: 2 min
---

At this point, we’ve successfully implemented login and logout capabilities using the Asgardeo provider for Auth.js. The next step is to explore how to access and display logged-in user details within the app utilizing the callbacks provided by auth.js library. To retrieve user information from the ID token provided by Asgardeo, the simplest approach is to use the JWT (JSON Web Token) returned during authentication. In auth.js, you can leverage the JWT callback function to access and manipulate this token. The JWT callback is triggered whenever a JWT is created or updated (e.g., at sign-in), making it a great place to include the user's information

Modified the code as below to see logged in user details.

```javascript title="auth.ts" hl_lines="14-29"

import NextAuth from "next-auth"
import Asgardeo from "next-auth/providers/asgardeo"

declare module "next-auth" {
  interface User {
    username?: string;
  }
}

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [Asgardeo({
    issuer: process.env.AUTH_ASGARDEO_ISSUER
  })],
  callbacks: {
    async jwt({ token, profile }) {
      if (profile) {
        token.username = profile.username;
      }

      return token;
    },
    async session({ session, token }) {            
      if (token) {
        session.user.username = token.username as string;
      }

      return session;
    }
  }
})

```
Auth.js is made to work with many identity providers and some of the objects/arguments are not valid or vary from one provider to another. In Asgardeo, by accessing the `profile` object in the `jwt` callback, we are able to get the information about the user using their decoded ID token information that is received from the profile object. 

Once this user information is returned from the `jwt` callback, we need to pass this data to the `session` object of the `auth()` function. To do that, we will be using the `session` callback. In the `session` callback, `session` is the object that is available in the `auth()` function and `token` object is the object returned from the `jwt` callback.




Then, update `page.tsx` with the following highlighted line to display the username of logged in user.  

```javascript title="page.tsx" hl_lines="4"

...
          <>
            <p> You are now signed in!</p>
            <p> hello {session.user?.username}</p>
            <form
              action={async () => {
                "use server"
                await signOut()
              }}
            >
              <button type="submit">Sign Out</button>
            </form>
          </>

...

```



If your Next.js application is already running in the development mode, the home page will be reloaded and you will see the updated user interface.

![Logout screen]({{base_path}}/complete-guides/nextjs/assets/img/image8.png){: width="800" style="display: block; margin: 0;"}



## Getting additional user attributes

By default, Asgardeo will only send the username in the ID token. But this can be configured in the Asgardeo console to send any user attribute in the ID token and then that will be available in the profile object.

To get additional user attributes to the ID token, the application should be configured to request the specific user attributes at the time of login. For example, if you want to retrieve a user's mobile number as an attribute, you need to configure the application to request the user’s mobile number as an attribute in the ID token.

1. Log in to the {{product_name}} console and select the application you created.
2. Go to the **User Attributes** tab, expand **Profile** section. 
3. Select the **First Name (given_name)**.
4. Select the **Last Name (family_name))**.
5. Click Update to save the changes.


 

Now, you need to modify the `auth.ts` with the required user attributes as shown in the following example.  


```javascript title="auth.ts" hl_lines="7-8 20-21 29-30"

import NextAuth from "next-auth"
import Asgardeo from "next-auth/providers/asgardeo"

declare module "next-auth" {
  interface User {
    username?: string;
    given_name?: string;
    family_name?: string;
  }
}

export const { handlers, signIn, signOut, auth } = NextAuth({
  providers: [Asgardeo({
    issuer: process.env.AUTH_ASGARDEO_ISSUER
  })],
  callbacks: {
    async jwt({ token, profile }) {
      if (profile) {
        token.username = profile.username;
        token.given_name = profile.given_name;
        token.family_name = profile.family_name;
      }

      return token;
    },
    async session({ session, token }) {            
      if (token) {
        session.user.username = token.username as string;
        session.user.given_name = token.given_name as string;
        session.user.family_name = token.family_name as string;
      }

      return session;
    }
  }
})

```

Since we are adding new information to the user object inside the `session object` (which is having the interface - User), note that we also have to update the interface to contain this new information.


Then, you can update `page.tsx` as given below to display the above user attributes.  

```javascript title="page.tsx" hl_lines="5-6"

...
          <>
            <p> You are now signed in!</p>
            <p> hello {session.user?.username}</p>
            <p> Given name:  {session.user?.given_name}</p>
            <p> Family name: {session.user?.family_name}</p>
            <form
              action={async () => {
                "use server"
                await signOut()
              }}
            >
              <button type="submit">Sign Out</button>
            </form>
          </>

...

```


!!! Tip

    If you don’t get any value for given_name and family_name, it might be because you have not added these values when creating the user in Asgardeo. You can add these values either using the **Asgardeo console** or logging into the **My Account** of that particular user.

In this step, we further improved our Next.js app to display the user attributes. As the next step, we will try to secure routes within the app.
