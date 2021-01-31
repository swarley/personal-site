require 'rake'
require 'liquid'
require 'filewatcher'

Liquid::Template.file_system = Liquid::LocalFileSystem.new(File.expand_path('app/_includes', __dir__))
VIEWS = Dir.children(File.expand_path('app/views', __dir__))
VIEW_DATA = VIEWS.map do |fname|
  [
    File.basename(fname, '.liquid'),
    Liquid::Template.parse(File.read(File.expand_path("app/views/#{fname}", __dir__)))
  ]
end.to_h

def compile
  mkdir_p 'out'
  cp_r Dir['public/*'], 'out'

  VIEW_DATA.each do |name, template|
    next if name == 'layout'

    page = name == 'home' ? '/' : "/#{name}"
    fname = name == 'home' ? 'index.html' : "#{name}.html"

    data = VIEW_DATA['layout'].render('content' => VIEW_DATA[name].render, 'page' => page)
    File.open(File.join('out', fname), 'w+') do |f|
      f.write(data)
    end
  end
end

desc 'Generate files to `out/`'
task :build do
  compile
end

desc 'Compile on file changes'
task :watch do
  compile

  Filewatcher.new(['public/**/*', 'app/**/*']).watch do
    puts 'Compiling'
    compile
  end
end

task :default => [:build]