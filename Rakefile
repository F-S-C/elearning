require 'asciidoc_publishing_toolbox'
require 'pathname'
require 'asciidoctor'
require 'asciidoctor-pdf'
require 'yaml'

task :default => [ :features ]do
    print "Building main document..."
    $stdout.flush
    AsciiDocPublishingToolbox.build dir: Pathname.new(__FILE__).dirname
    puts '    Done'
end

task :features do
    print "Building 'features'..."
    $stdout.flush
    authors = Hash.new
    authors[:names] = YAML.load_file('document.yml')['authors'].each_with_index.map {|a, i| "#{a['name']} #{a['surname']}"}
    authors[:emails] = YAML.load_file('document.yml')['authors'].each_with_index.map {|a, i| a['email']}
    Asciidoctor.convert_file 'src/analisi-delle-piattaforme.adoc', backend: 'pdf', safe: :unsafe, attributes: { 'doctype'=> 'article', 'title-page' => true, 'media'=>'prepress', 'pdf-themesdir'=>'themes', 'pdf-theme'=>'article', 'author' => authors[:names]}, to_dir: 'out', to_file: 'relazione-preliminare.pdf'
    puts '    Done'
end
