require 'rubygems'
require 'digest/md5'
require 'rake/contrib/ftptools'

# This file stores $ftp_login and $ftp_password which are used for uploading.
if File.exist?('Rakefile.config')
  load 'Rakefile.config'
end

task :default => [:lessc]

desc 'Watch Less'
task :watch do
  system('when-changed _less/* -c rake lessc')
end

desc 'Compile Less'
task :lessc do
  rm Dir.glob('css/*.css')
  mkdir_p 'css'
  system('lessc --yui-compress "_less/main.less" > "css/intermediate.css"')
  hash = Digest::MD5.file('css/intermediate.css').hexdigest()
  mv 'css/intermediate.css', 'css/'+hash+'.css'
  system('find . -name "*.html" -exec sed -i "s/<link rel=\"stylesheet\" href=\"css\/.*\.css\">/<link rel=\"stylesheet\" href=\"css\/'+hash+'\.css\">/g" {} \;')
end

desc 'Running Jekyll with --server --auto opition'
task :dev do
  system('jekyll --server --auto')
end

task :upload => :lessc do
	system('jekyll --url http://business-english-bocholt.de --base-url /')
	cd '_site' do
  	Rake::FtpUploader.connect('/html/business-english', $ftp_server, $ftp_login, $ftp_password) do |ftp|
    	ftp.verbose = true # gives you some output
    	ftp.upload_files("./**/*")
      ftp.upload_files(".htaccess")
  	end
	end
end
