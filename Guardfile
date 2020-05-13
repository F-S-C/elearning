Bundler.require :default

rake = Rake.application
rake.init
rake.load_rakefile

guard 'shell' do
  watch(/^.*?\.adoc$/) do |m|
    puts Pathname.new(m[0]).dirname.dirname
    rake['main'].invoke
    rake['main'].reenable
  end
end
