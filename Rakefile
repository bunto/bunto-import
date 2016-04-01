require 'rubygems'
require 'bundler/gem_tasks'
require 'rake'
require 'rdoc'
require 'date'
require 'yaml'

#############################################################################
#
# Helper functions
#
#############################################################################

def name
  @name ||= Dir['*.gemspec'].first.split('.').first
end

def version
  BuntoImport::VERSION
end

def normalize_bullets(markdown)
  markdown.gsub(/\s{2}\*{1}/, "-")
end

def linkify_prs(markdown)
  markdown.gsub(/#(\d+)/) do |word|
    "[#{word}]({{ site.repository }}/issues/#{word.delete("#")})"
  end
end

def linkify_users(markdown)
  markdown.gsub(/(@\w+)/) do |username|
    "[#{username}](https://github.com/#{username.delete("@")})"
  end
end

def linkify(markdown)
  linkify_users(linkify_prs(markdown))
end

def liquid_escape(markdown)
  markdown.gsub(/(`{[{%].+[}%]}`)/, "{% raw %}\\1{% endraw %}")
end

def remove_head_from_history(markdown)
  index = markdown =~ /^##\s+\d+\.\d+\.\d+/
  markdown[index..-1]
end

def converted_history(markdown)
  remove_head_from_history(liquid_escape(linkify(normalize_bullets(markdown))))
end

#############################################################################
#
# Standard tasks
#
#############################################################################

task :default => :test

require 'rake/testtask'
Rake::TestTask.new(:test) do |test|
  test.libs << 'lib' << 'test'
  test.pattern = 'test/**/test_*.rb'
  test.verbose = true
end

require 'rdoc/task'
Rake::RDocTask.new do |rdoc|
  rdoc.rdoc_dir = 'rdoc'
  rdoc.title = "#{name} #{version}"
  rdoc.rdoc_files.include('README*')
  rdoc.rdoc_files.include('lib/**/*.rb')
end

desc "Open an irb session preloaded with this library"
task :console do
  sh "irb -rubygems -r ./lib/#{name}.rb"
end

#############################################################################
#
# Site tasks - http://bunto.github.io/import/
#
#############################################################################

namespace :site do
  desc "Generate and view the site locally"
  task :preview do
    require "launchy"

    # Yep, it's a hack! Wait a few seconds for the Bunto site to generate and
    # then open it in a browser. Someday we can do better than this, I hope.
    Thread.new do
      sleep 4
      puts "Opening in browser..."
      Launchy.open("http://localhost:4000")
    end

    # Generate the site in server mode.
    puts "Running Bunto..."
    Dir.chdir("site") do
      sh "bunto serve --watch"
    end
  end

  desc "Update normalize.css library to the latest version and minify"
  task :update_normalize_css do
    normalize_file = "site/_sass/normalize.scss"
    sh "curl \"http://necolas.github.io/normalize.css/latest/normalize.css\" -o #{normalize_file}"
    sh "sass #{normalize_file}:#{normalize_file} --style compressed"
  end

  desc "Commit the local site to the gh-pages branch and publish to GitHub Pages"
  task :publish => [:history] do
    sh "git subtree push --prefix site origin gh-pages"
  end

  desc "Create a nicely formatted history page for the bunto site based on the repo history."
  task :history do
    if File.exist?("History.markdown")
      history_file = File.read("History.markdown")
      front_matter = {
        "layout" => "docs",
        "title" => "History",
        "permalink" => "/docs/history/",
        "prev_section" => "contributing"
      }
      Dir.chdir('site/_docs/') do
        File.open("history.md", "w") do |file|
          file.write("#{front_matter.to_yaml}---\n\n")
          file.write(converted_history(history_file))
        end
      end
      unless `git diff site/_docs/history.md`.strip.empty?
        sh "git add site/_docs/history.md"
        sh "git commit -m 'Updated generated history.md file in the site.'"
      else
        puts "No updates to commit at this time. Skipping..."
      end
    else
      abort "You seem to have misplaced your History.markdown file. I can haz?"
    end
  end
end
