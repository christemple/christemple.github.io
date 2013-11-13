require 'rake'

def application_process_id
  `lsof -i tcp:4000 | tail -n 1 | awk '{print $2}'`
end

def application_running?
  not application_process_id.empty?
end

def start_application!
	system "jekyll serve --watch"
end

def stop_application!
	`kill -9 #{application_process_id}`
end

desc 'Jekyll: Rebuild and serve'
task :default do
	stop_application! if application_running?
	start_application!
end

desc 'Create a new post'
task :new do
	puts "Post title: "
	title = STDIN.gets.chomp
	post_file_name = "#{Date.today.strftime("%Y-%m-%d")}-#{title.downcase.gsub " ", "-"}.markdown"
	File.open("_posts/#{post_file_name}", "w") do |file|
		file.puts "---"
		file.puts "layout: post"
		file.puts "title: #{title}"
		file.puts "date: #{Time.new.strftime("%Y-%m-%d %H:%M:%S")}"
		file.puts "---\n\n"
	end
end