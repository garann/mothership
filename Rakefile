git_repo = ENV["git_repo"] || "git@github.com:garann/mothership"
deploy_dir = "_deploy"
deploy_branch = "gh-pages"
public_dir = "_site"

desc "initialize the deployment directory"
task :init_github do
  system "mkdir -p #{deploy_dir}"
  cd "#{deploy_dir}" do
    puts "\n## Creating #{deploy_dir} directory"
    system "git init"
    system "git remote add origin #{git_repo}"
    system "echo 'Coming soon... real soon' > index.html"
    system "git add . && git commit -m 'Releasing!'"
    system "git branch -m gh-pages"
  end
end

multitask :deploy do
  Rake::Task[:generate].invoke()
  Rake::Task[:push].invoke()
end

desc "generate the site using jekyll"
task :generate do
  system "jekyll"
end

desc "deploy public directory to github pages"
task :push do
  puts "## Deploying branch to Github Pages "
  (Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
  Rake::Task[:copydot].invoke(public_dir, deploy_dir)
  puts "\n## copying #{public_dir} to #{deploy_dir}"
  cp_r "#{public_dir}/.", deploy_dir
  puts "\n## copying CNAME to #{deploy_dir}"
  cd "#{deploy_dir}" do
    system "git add ."
    system "git add -u"
    puts "\n## Commiting: Site updated at #{Time.now.utc}"
    message = "Site updated at #{Time.now.utc}"
    system "git commit -m \"#{message}\""
    puts "\n## Pushing generated #{deploy_dir} website"
    system "git push origin #{deploy_branch} --force"
    puts "\n## Github Pages deploy complete"
  end
end

desc "copy dot files for deployment"
task :copydot, :source, :dest do |t, args|
  FileList["#{args.source}/**/.*"].exclude("**/.", "**/..", "**/.DS_Store", "**/._*", "**/.git*").each do |file|
    cp_r file, file.gsub(/#{args.source}/, "#{args.dest}") unless File.directory?(file)
  end
end
