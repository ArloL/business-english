require 'rubygems'
require 'digest/md5'
require 'jekyll'
require 'rake/contrib/ftptools'

# This file stores $ftp_login and $ftp_password which are used for uploading.
if File.exist?('Rakefile.config')
  load 'Rakefile.config'
end

task :default => [:serve]

desc 'Serve jekyll site'
task :serve do
  Jekyll::Commands::Build.process(Jekyll.configuration({:config => '_config.dev.yml', :serving => true, :watch => true}))
  Jekyll::Commands::Serve.process(Jekyll.configuration({:config => '_config.dev.yml', :serving => true, :watch => true}))
end

desc 'Upload the website to the live server'
task :upload do
  Jekyll::Commands::Build.process(Jekyll.configuration({:config => '_config.live.yml'}))
  cd '_site' do
    hash = Digest::MD5.file('styles.css').hexdigest() + '.css'
    mv 'styles.css', hash
    replace_stylesheet_links(hash)
    Rake::FtpUploader.connect('/html/business-english', $ftp_server, $ftp_login, $ftp_password) do |ftp|
      ftp.verbose = true # gives you some output
      ftp.upload_files("./**/*")
      ftp.upload_files(".htaccess")
    end
  end
end

def replace_stylesheet_links(new_name)
  files = Dir.glob('{**/*.html}')
  files.each{ |arg|
    content = open(arg).gets(nil)
    if content == nil then
      next
    end
    content = content.gsub(/<link rel=\"stylesheet\" href=\"styles\.css\">/, '<link rel="stylesheet" href="' + new_name + '">')
    open(arg, 'w'){ |file|
      file.puts(content)
      file.flush
    }
  }
end
