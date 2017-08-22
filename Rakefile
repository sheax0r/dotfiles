task :default do
	run %{rake -T}
end

desc "Do everything"
task all: %i{yadr brew ruby gem_sources gems github_programs link}

task :yadr do
	run %{sh -c "`curl -fsSL https://raw.githubusercontent.com/skwp/dotfiles/master/install.sh`"}
end

# Install brews
desc "Install brews"
task :brew do
	run %{brew install chruby ruby-build watch awscli}
end

desc "Install ruby versions"
task :ruby do
	ruby_versions.each do |version|
		next if ruby_installed?(version)
		run %{ruby-build #{version} ~/.rubies/ruby-#{version}}
	end
end

desc "Install gem sources"
task :gem_sources do
	puts "When you hit ENTER, a browser window will open. Do the oauth dance, then paste the contents of the browser window here."
	STDIN.gets
	run %{open "https://gemgate-heroku-internal-gems.herokuapp.com/setup-instructions"}
	cmd = STDIN.gets
	run(cmd)
end

desc "Install gems in each ruby version"
namespace :gems do
	task :install do
		ruby_versions.each do |version|
			run %{chruby-exec #{version} -- gem install #{gems.join(" ")}}
		end
	end

	task :update do
		ruby_versions.each do |version|
			run %{chruby-exec #{version} -- gem update #{gems.join(" ")}}
		end
	end
end

desc "Links"
task :link do
	home = ENV["HOME"]
	dotfiles = "#{home}/.dotfiles"

	# Link vim stuff
	link_file(File.join(File.dirname(__FILE__)), dotfiles)
	link_file("#{dotfiles}/.vimrc.d", "#{home}/.vimrc.d")
	link_file("#{dotfiles}/.vimrc.after", "#{home}/.vimrc.after")

	# Link zsh stuff
	link_file("#{dotfiles}/.zsh.after", "#{home}/.zsh.after")
	link_file("#{dotfiles}/.zsh.before", "#{home}/.zsh.before")
end

def link_file(target, pointer)
	FileUtils.rm_f(pointer)
	FileUtils.ln_s(target, pointer)
end

# Install programs from github source
desc "Install programs from github sources"
task :github_programs do
	run %{cd ~/git;  git clone https://github.com/sampson-chen/sack.git; cd sack; chmod +x install_sack.sh && ./install_sack.sh}
end

desc "Configure zsh"
task :zsh do
	FileUtils.ln_s(File.join(File.dirname(__FILE__), ".zsh.after"), "#{ENV["HOME"]}/.zsh.after")
	FileUtils.ln_s(File.join(File.dirname(__FILE__), ".zsh.before"),"#{ENV["HOME"]}/.zsh.before")
end

private

# List of ruby versions to install
def ruby_versions
	%w{
		2.3.1
		2.3.4
	}
end

# Check if the given ruby is installed or not
def ruby_installed?(version)
	File.exist?("#{ENV["HOME"]}/.rubies/ruby-#{version}")
end

# List of gems to install globally
def gems
	%w{gpgenv ion-client pry}
end

def run(cmd)
  puts "[Running] #{cmd}"
  `#{cmd}` unless ENV['DEBUG']
end
