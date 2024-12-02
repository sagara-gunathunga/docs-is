---
template: templates/complete-guide.html
heading: Securing Routes within the app
read_time: 2 min
---
Assume we have a `<Profile/>` component that should only be accessible when a user is logged in. Because if a valid user is not in the session, there is no point of showing an empty profile page. Therefore we need to secure the route: http://localhost:3000/profile itself. This can vary depending on the router that you are using. In this example, we will be using the Next router to demonstrate how to secure a route using auth.js.


## Create a Higher-Order Component (HOC) - withProtectedRoute

A higher-order component in React is a function that takes a component and returns a new component. The HOC `withProtectedRoute` will check if a user is authenticated and either render the component or redirect the user to the login page.

This can be achieved by using the status object in the `useSession()` hook provided by Auth.js. The status object can have three values depending on the authenticated state.

- **authenticated** - User is authenticated and contains a valid session
- **unauthenticated** - User is NOT authenticated and does not have a valid session
- **loading** - Auth.js is still processing the request and is in an intermediate loading state. This useful to show proper loading screens in the UI and avoid inconsistencies.

Depending on these states, let’s create the `withProtectedRoute` using the following code:


```javascript title="components/with-protected-component.tsx"

import { useSession } from 'next-auth/react';
import { useRouter } from 'next/navigation';
import { useEffect } from 'react';

export const withProtectedRoute = (WrappedComponent: React.ComponentType) => {

  const ComponentWithAuth = (props: React.ComponentProps<typeof WrappedComponent>) => {
    const { status } = useSession();
    const router = useRouter();

    useEffect(() => {        
      // If there is no session, redirect to the index page
      if (status === "unauthenticated") {
        router.push('/');
      }
    }, [router, status]);

    if (status === "loading") {
      return <p>Loading...</p>;
    }

    // If the user is authenticated, render the WrappedComponent
    // Otherwise, render null while the redirection is in progress
    return status === "authenticated" ? <WrappedComponent {...props} /> : null;
  };

  return ComponentWithAuth;
};


```

The HOC-withProtectedRoute first checks if the user is authenticated, unauthenticated, or still in a loading state. If the status is `unauthenticated`, meaning the user does not have a valid session, the HOC uses the `useRouter` hook from Next.js to programmatically redirect the user to the home page (or any other page you specify). This ensures that users who are not logged in cannot access protected content.

While auth.js is still determining the user's status, the component shows a simple loading message (Loading...) to prevent the protected component from being rendered prematurely. Once the status is confirmed as authenticated, meaning the user is successfully logged in and has a valid session, the HOC renders the wrapped component and passes any props to it as intended. If the user is not authenticated, the component does not render, and the redirection takes place smoothly.

This approach allows you to manage authentication checks and redirection in one place, simplifying the process of protecting multiple components across your app. By using this HOC, you can easily control access to pages, ensuring that sensitive content is only available to logged-in users while keeping the rest of the app running seamlessly.

It can be applied to the Profile component as follows:

```javascript title="app/profile/page.tsx"

"use client";

import { SignOutButton } from "@/components/sign-out-button";
import { withProtectedRoute } from "@/components/with-protected-route";
import { useSession } from "next-auth/react";

const Profile = () => {
    const { data: session } = useSession()

    if (!session) {
        return (
            <div className="h-screen w-full flex items-center justify-center">
                <h1>You need to sign in to view this page</h1>
            </div>
        );
    }

    return (
        <div className="h-screen w-full flex flex-col items-center justify-center">
            <h1 className="mb-5">Profile Page</h1>
            <p>Email : {session?.user?.email}</p>
            <p>First Name : {session?.user?.given_name}</p>
            <p>Last Name : {session?.user?.family_name}</p>
            <div className="mt-5">
                <SignOutButton />
            </div>
        </div>
    );
}

export default withProtectedRoute(Profile);

```

Now verify that you cannot access http://localhost:3000/profile URL when you are not logged in. You will be redirected to http://localhost:3000 if you do not have a valid user logged in.


In this step, we looked into how to secure component routes within a Next.js app. Next, we will try to access a protected API from our Next.js app, which is a common requirement.



