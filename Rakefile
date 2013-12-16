require 'rubygems'
require 'digest/md5'
require 'directory_watcher'
require 'jekyll'
require 'less'
require 'rake/contrib/ftptools'

# This file stores $ftp_login and $ftp_password which are used for uploading.
if File.exist?('Rakefile.config')
  load 'Rakefile.config'
end

task :default => [:serve]

desc 'Hash css file, rename it and replace all stylesheet links'
task :prod_less => :less do
  hash = Digest::MD5.file('css/intermediate.css').hexdigest() + '.css'
  mv 'css/intermediate.css', 'css/'+hash
  replace_stylesheet_links(hash)
end

desc 'Replace all stylesheet links to intermediate'
task :dev_less => :less do
  replace_stylesheet_links('intermediate.css')
end

desc 'Compile less files'
task :less do
  rm Dir.glob('css/*.css')
  mkdir_p 'css'
  parser = Less::Parser.new :paths => '_less', :filename => '_less/main.less'
  tree = parser.parse(open('_less/main.less').gets(nil))
  css = tree.to_css(:compress => true)
  f = open('css/intermediate.css', 'w')
  f.puts(css)
  f.flush
end

desc 'Serve jekyll site and automatically compile less files'
task :serve => :dev_less do
  dw = DirectoryWatcher.new '_less', :glob => '*.less', :pre_load => true, :interval => 1
  dw.add_observer {
    Rake::Task['less'].reenable
    Rake::Task['dev_less'].reenable
    Rake::Task['dev_less'].invoke
  }
  dw.start
  Jekyll::Commands::Build.process(Jekyll.configuration({:config => '_config.dev.yml', :serving => true, :watch => true}))
  Jekyll::Commands::Serve.process(Jekyll.configuration({}))
end

def replace_stylesheet_links(new_name)
  files = Dir.glob('{*.html,_includes/*.html,_layouts/*.html}')
  files.each{ |arg|
    content = open(arg).gets(nil)
    content = content.gsub(/<link rel=\"stylesheet\" href=\"css\/.*\.css\">/, '<link rel="stylesheet" href="css/' + new_name + '">')
    open(arg, 'w'){ |file|
      file.puts(content)
      file.flush
    }
  }
end

desc 'Upload the website to the live server'
task :upload => :prod_less do
  Jekyll::Commands::Build.process(Jekyll.configuration({:config => '_config.live.yml'}))
  cd '_site' do
    Rake::FtpUploader.connect('/html/business-english', $ftp_server, $ftp_login, $ftp_password) do |ftp|
      ftp.verbose = true # gives you some output
      ftp.upload_files("./**/*")
      ftp.upload_files(".htaccess")
    end
  end
end
