# Heroku deployment!!!

### What is Heroku?

- A cloud Platform-as-a-Service (PAAS), used to deploy and manage websites and applications
- Takes the pain out of configuring servers
- Can have collaborators
- Can use addons, like a Postgres database
- "Heroku can be a great fit for teams who want to do zero dev ops or create small apps"

### Steps to deploy

1. Go to [heroku.com](https://heroku.com) and create an account.

2. Install the heroku command line utility by running `brew install heroku`.

3. Run the command `heroku login` and log in using the email and password you signed up with.

4. Run `heroku create` in the root of your project folder.

5. Run `heroku open`, which will open your web app in your browser (if it doesn't work just skip it for now). You should see a welcome webpage with the text => Heroku | Welcome to your new app!

6. Heroku will have its own version of your git repo on its server. So, you have to push to it by running `git push heroku master`. If you get an error here, just keep going and we'll fix the error in a later step. **NOTE: any time you make changes to your repo, you'll have to push it to Heroku and GitHub.**

7. Go to the [dashboard on heroku.com](https://dashboard.heroku.com/apps) and select your newly created app.

8. Click on the configure add-ons link, then in the add-ons search box look for 'postgres' and install it.

9. **NOTE: this is for authentication only, if you're not using authentication, you don't need to do this.** Click on the 'settings' tab in the dashboard. Click on the 'Reveal Config Vars' button. Add a new config var, for the key enter 'SECRET_KEY' and for the value enter a long random string. Remember when we added this to the `.env` file? We can't push the `.env` file since we .gitignored it. This is how we add the same config variable without using the .env file.

10. We have to change our `db/config.js` file to make sure `pg-promise` works with heroku. The file should now look like this:

```javascript
const pgp = require('pg-promise')();

let db;

if (process.env.NODE_ENV === 'development' || !process.env.NODE_ENV) {
  db = pgp({
    database: 'adaquote_development',
    port: 5432,
    host: 'localhost',
  });
} else if (process.env.NODE_ENV === 'production') {
  db = pgp(process.env.DATABASE_URL);
}

module.exports = db;
```
11. After making these changes ^ , you'll have to `git add`, `git commit`(you know the drill), ** `git push heroku master`**. 
> IF YOU GET AN ERROR!!!!!! 
> Something along the lines of ... "could not find remote heroku", you need to add it! (Don't worry if you don't understand it now, we will explain in unit 3).
> in your terminal, you need to run `heroku info`
> See 'Git URL'? Copy the URL
> Run `git remote add heroku PASTE_URL_THAT_YOU_JUST_COPIED_HERE`
> To check if it worked, type `git remote -v` -> if any of the lines include `heroku` word, you are good to go!
> Now you are ready to `git add`, `git commit`, `git push heroku master` !!!


11. Before step 12, you have to remove any `\connects` or `\c`s from your sql file migrations and seeds. The database name on heroku is something heroku makes up, so you can't connect to a different name. **Again, remove the `\connect`s and `\c`s from you sql files. If you need to later run those files on your localhost, you'll need to re-add those connections.**

12. We're almost done. We just need to run our migration files to set up the database and any seed files if we have any. So far, we've been doing `psql -f [filename].sql`. We have to do it a little differently now. First, you need to navigate to the migrations or seeds directory. Then you can run any or all your `.sql` files by running this command: `heroku pg:psql --app app_name < file.sql`. **NOTE: you have to change `file.sql` to the actual filename you want to run, like `migration_05021984` or `seed.sql`. You also have to add your own app's name. To find out your app's name run `heroku info`, it's the name next to === up at top.**
