task :default => :new

require 'fileutils'

desc "创建新 post"
task :new do
 puts "请输入要创建的 post URL："
	@url = STDIN.gets.chomp
	puts "请输入 post 标题："
	@name = STDIN.gets.chomp
 	puts "请输入 post 简介："
 	@subtitle = STDIN.gets.chomp
	puts "请输入 post 分类，以，分隔："
	@categories = STDIN.gets.chomp
	puts "请输入 post 标签，以，分隔："
	@tag = STDIN.gets.chomp
	@slug = "#{@url}"
	@slug = @slug.downcase.strip.gsub(' ', '-')
    @categories = "[#{@categories}]"
    @tag = "[#{@tag}]"
	@date = Time.now.strftime("%F")
	@post_name = "_posts/#{@date}-#{@slug}.md"
	if File.exist?(@post_name)
			abort("文件名已经存在！创建失败")
	end
	FileUtils.touch(@post_name)
	open(@post_name, 'a') do |file|
			file.puts "---"
			file.puts "layout: post"
			file.puts "title: #{@name}"
			file.puts "categories: #{@categories}"
#		file.puts "author: pizida"
#			file.puts "date: #{Time.now}"
#			file.puts "categories: #{@categories}"
			file.puts "tags: #{@tag}"
            file.puts "description: #{@subtitle}"
			file.puts "---"
	end
#	exec "vi #{@post_name}"
end