# Canadian Legislative Scrapers

This Django project runs the [Canadian legislative scrapers](http://github.com/opencivicdata/scrapers-ca), displays the status of each scraper, and returns the scraped data as JSON.

## Development

To install dependencies, see the instructions in [blank-pupa](https://github.com/opennorth/blank-pupa).

Set up the git repository:

```
git clone git@github.com:opennorth/scrapers_ca_app.git
git submodule init
git submodule update
```

Switch the submodule to master:

```
cd scrapers
git checkout master
cd ..
```

Create a virtual environment:

```
rm -rf ~/.virtualenvs/scrapers_ca_app # if it already exists
mkvirtualenv scrapers_ca_app
pip install -r requirements.txt
```

Create a database (`dropdb pupa` if it already exists):

```
dropdb pupa
createdb pupa
python manage.py syncdb --noinput
```

Drop the MongoDB database:

```
mongo pupa --eval 'db.dropDatabase()'
```

Run all the scrapers:

```
python cron.py
```

Or run specific scrapers:

```
python cron.py ca_ab_edmonton ca_ab_grande_prairie_county_no_1
```

Install the foreman gem:

```
gem install foreman
```

Start the web app:

```
foreman start
```

## Data Quality

```
mkdir test
```

```
mongo pupa sanity/common.js sanity/contact_details.js > test/contact_details.txt
mongo pupa sanity/common.js sanity/links.js > test/links.txt
mongo pupa sanity/common.js sanity/names.js sanity/posts.js sanity/styles.js sanity/miscellaneous.js > test/miscellaneous.txt
mongo pupa sanity/common.js sanity/relations.js > test/relations.txt
mongo pupa sanity/common.js sanity/styles.js sanity/roles.js > test/roles.txt
```

```
diff -U0 sanity/pass/contact_details.txt test/contact_details.txt
diff -U0 sanity/pass/links.txt test/links.txt
diff -U0 sanity/pass/relations.txt test/relations.txt
diff -U0 sanity/pass/roles.txt test/roles.txt
cat test/miscellaneous.txt
```

## Deployment

Add configuration variables (replace `DATABASE`):

```
heroku config:set PRODUCTION=1
heroku config:set DJANGO_SECRET_KEY=your-secret-key
heroku config:set DATABASE_URL=`heroku config:get DATABASE`
```

Setup the database (replace `DATABASE`):

```
heroku pg:reset DATABASE
heroku run python manage.py syncdb --noinput
```

Add `python cron.py` to the [Heroku Scheduler](https://scheduler.heroku.com/dashboard).

## Represent

Run `python represent.py` to see a list of scrapers that can be integrated into Represent.

## Troubleshooting

* Make sure PostgreSQL and MongoDB are running. If you use Homebrew, you can find instructions on how to run each with:

```
brew info postgres
brew info mongo
```

## Bugs? Questions?

This repository is on GitHub: [http://github.com/opennorth/scrapers_ca_app](http://github.com/opennorth/scrapers_ca_app), where your contributions, forks, bug reports, feature requests, and feedback are greatly welcomed.

Copyright (c) 2013 Open North Inc., released under the MIT license
