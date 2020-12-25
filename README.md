In this article, I will show you how to link your Angular app repo with a server. So whenever you push your changes to a specific branch, the server will automatically build and configure your app based on the scripts you defined and publish the app on the fly.

## 1. Creating an Angular App using [Angular CLI] (https://cli.angular.io/)

> Or you can download the final result [HERE](https://github.com/YoussefZidan/angular-ci-app)

```
ng new angular-ci-app
```

> Make sure to choose `Yes` when it asks you to create the app with the Routing.

## 2. Creating a new [GitHub](https://github.com/new) Repo.

![Creating Github Repo](https://dev-to-uploads.s3.amazonaws.com/i/2qy8pbu2v3cetss6jb77.JPG)

> If you wonder why my GitHub is on a dark theme check this [Chrome extension] (https://chrome.google.com/webstore/detail/github-dark-theme/odkdlljoangmamjilkamahebpkgpeacp)

## 3. Open your created app on [VS Code] (https://code.visualstudio.com)

Open the terminal and push your app to the newly created repo

```
git remote add origin https://github.com/<Your-User-Name>/<Repo-Name>.git
git branch -M master
git push -u origin master
```

## 4. Cleaning up and Adding a simple app with Routes.

- Open App.Component.html and delete everything inside and paste the following code.

App.Component.html

```html
<style>
  button {
    margin-right: 20px;
  }
</style>
<button routerLink="/home">Home</button>
<button routerLink="/about">about</button>

<router-outlet></router-outlet>
```

- Open app-routing.module.ts and paste the following code

app-routing.module.ts

```ts
import { NgModule } from "@angular/core";
import { Routes, RouterModule } from "@angular/router";
import { HomeComponent } from "./components/home/home.component";
import { AboutComponent } from "./components/about/about.component";

const routes: Routes = [
  { path: "", redirectTo: "/home", pathMatch: "full" },
  {
    path: "home",
    component: HomeComponent,
  },
  {
    path: "about",
    component: AboutComponent,
  },
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
})
export class AppRoutingModule {}
```

- Create a new folder inside the app folder and name it components and create the `Home` and the `About` components.

![Home and About components](https://dev-to-uploads.s3.amazonaws.com/i/6492f2yl9e5vkazd228k.JPG)

- Push your changes to the repo

```
git add .
git commit -m "creating routes"
git push
```

## 5. Linking the repo to [Netlify](https://www.netlify.com/)

Create a new account on Netlify and follow along with me.

- Click on New Site From Git Button
- Choose GitHub Option

> Netlify will ask for permission

- Search for your repo name
- Pick a branch to deploy

> The selected branch will be the one which Netlify will be listening on it and whenever you push a change to it Netlify will build your app based on the configuration we will make below.
> So, I will choose `master` for now which is the only branch we currently have.

- Build command

> Type the build command of your app, In our case, it will be `ng build`

- Publish Directory

> By default Angular build your application inside a `dist` folder
> and will create another folder inside of it with the `name` of your app (You will find it inside `package.json`) in my case its `angular-ci-app`
> So, I will type `dist/angular-ci-app`

![Configuring Netlify](https://dev-to-uploads.s3.amazonaws.com/i/lqcm50wt61letyi9cp2r.JPG)

- Finally, Click on Deploy Site.

![deploying](https://dev-to-uploads.s3.amazonaws.com/i/bmvhim5jc8r1f1wzarac.JPG)

## 6. Previewing your app

![Preview App](https://dev-to-uploads.s3.amazonaws.com/i/24zkij34mz6y3xzhhvvi.JPG)

By default Netlify will give a dummy URL name of your app, You can change it if you want from `Site Settings`

# 7. Fixing 404 Page Issue

If you copy the URL with any route for example /home and paste it, this will redirect you to the 404 page.
To solve this problem we have two solutions.

### First Solution:

Create `_redirects` file inside `src` folder and paste the following line:

```
/*  /index.html  200
```

then open `angular.json` file search for the assets array => `projects > architect > options > assets`
and add `"src/_redirects"`

angular.json

```json
"projects": {
    //...
      "architect": {
        "build": {
          //..
          "options": {
            //..
            "assets": ["src/favicon.ico", "src/assets", "src/_redirects"],

```

### Second Solution:

Put `{ useHash: true }` as a second argument for the `forRoot` function inside `app.routing.module` as the following

```ts
// ...
@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: true })],
  exports: [RouterModule],
})

// ...
```

This will prefix the routes by adding #.

So, the routes will be something like `http://localhost:4200/#/home`

# 8. Pushing to the master branch and watch for the changes.

```
git add .
git commit -m "fixing 404 page"
git push
```

Go to Netlify and you will find it started to build your app again automatically.

Now, when you copy and paste the URL you will no longer be directed to the 404 page.

> See my final Result https://angular-ci.netlify.app/#/home

# Tips

I will tell you some tips that I personally use on my own projects

- I usually create a `demo` branch and link this branch to Netlify.
- I create another branch `dev` and I commit and push my changes on it and when I want to publish my changes to the client I merge the `dev` branch into the `demo` branch and push the changes.
