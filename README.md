
# Deploying API docs

1. Clone this repo inside the textus web app repo: `git clone https://github.com/TextUsBiz/slate.git docs/slate`
1. Generate the docs from within the textus web app: `rake api_docs:generate`
1. In the `docs/slate` folder, commit the change and push: `git commit -Am "Update index.html.mkd" && git push`
1. Deploy the changes to http://developer.textus.com: `bundle install && ./deploy.sh`


