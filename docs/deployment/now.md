# Deploy to now.sh

Deployment on now.sh needs a few tweaks.
To deploy on [`now`](https://now.sh), you would need an external MongoDB cloud provider, like [`mongolab`](https://mlab.com) or [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).

### Clone and prepare the source code repository
```bash
$ git clone https://github.com/errbit/errbit.git
cd errbit
```

### Setup your secrets in our `now` account.
```bash
$ now secrets add mongo-url mongodb://my-mongo-host

# Setup your application secret
$ now secrets add secret-key-base $(rake secret)

# Optional secrets
$ now secrets add smtp-domain smtp.some-provider.com
$ now secrets add smtp-username myusername
$ now secrets add smtp-password mypassword
```

### Setup a `.env` file
Create a `.env` to store your configurations. Allowed envs are stored in the `.env.default` file. You can refer your secrets by prefixing them with a `@` (i.e. @mongo-url)

Here's a minimal example:
```bash
RACK_ENV=production
SECRET_KEY_BASE=@secret-key-base
ERRBIT_EMAIL_FROM='errbit@example.com'
MONGO_URL=@mongo-url
```
 
### Run the bootstrap command
Since you can't run ssh in the now environment, running the bootstrap script could be pretty tricky. There could be two options:

#### 1) Run it on your local environment

```bash
$ bundle install
$ MONGO_URL=mongodb://my-mongo-host bundle exec rake errbit:bootstrap
```

#### 2) Add it to the Dockerfile for the first run

In the `Dockerfile`, add this:

```Docker
RUN bundle exec rake errbit:bootstrap
```

Just after this line:

```Docker
USER errbit
```

Then remove that line after the first deploy.

### Run the deploy command:

```bash
$ now --public --dotenv
```
