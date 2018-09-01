# pull env variables for easier access
travis = ENV['TRAVIS']
travis_pr = ENV['TRAVIS_PULL_REQUEST']
source_commit = ENV['TRAVIS_COMMIT']
token = ENV['GITHUB_OAUTH_TOKEN']

# task configuration
deploy_path = "_site"
if travis == "true"
  deploy_repo = "https://potentialize:#{token}@github.com/potentialize/potentialize.github.io.git"
else
  deploy_repo = "git@github.com:potentialize/potentialize.github.io.git"
end
deploy_branch = "master"
deploy_user_name = "Deploy script"
deploy_user_email = "38586343+potentialize@users.noreply.github.com"

# tasks
desc "Start Jekyll dev server with browser auto reloading"
task :dev do
  sh "bundle exec jekyll serve --livereload" # include deploy_path?
end

desc "Create a new post"
task :newp do
  title = ARGV
    .drop(1)
    .join(' ')

  perma_title = title
    .gsub(' ', '-')
    .downcase

  date = Time.now

  file_name = "_posts/%s-%02d-%02d-%s.md" % [date.year, date.month, date.day, perma_title]

  if File.file? file_name
    puts "'#{file_name}' already exists! Aborting..."
  else
    post = File.new(file_name, 'w')
    post.puts "---"
    post.puts "excerpt: "
    post.puts "image: /images/"
    post.puts "---"
    post.puts "\# #{title}"
    post.close

    puts "Successfully created '#{file_name}'"
    puts "Have fun writing '#{title}'!"
  end

  exit
end

desc "Run bundle install"
task :install do
  sh "bundle install"
end

desc "Build the site"
task :build do
  sh "bundle exec jekyll build" # include deploy_path?
end

desc "Clean site folder"
task :clean do
  sh "rm -rf #{deploy_path}"
end

desc "Pull in production repo so deploy task can track history and push changes"
task :prepare_build_destination do
  sh "git clone --depth 1 --branch #{deploy_branch} #{deploy_repo} #{deploy_path}"
end

desc "Deploy site"
task :deploy do
  if travis_pr != nil && travis_pr != "false"
    puts "Pull request detected. Not proceeding with deploy."
    exit
  end

  git_args = "--work-tree #{deploy_path} --git-dir #{deploy_path}/.git"
  sh "git #{git_args} add ."
  sh "git #{git_args} commit -m \"Build commit #{source_commit}\""
  sh "git #{git_args} push #{deploy_repo} #{deploy_branch}:#{deploy_branch}"
end
