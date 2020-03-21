require "pathname"
require "asciidoctor"
require "asciidoctor-pdf"
require "asciidoc_publishing_toolbox"
require "yaml"
require "net/http"

def build_asciidoc(source, opts = {})
  authors = Hash.new
  authors[:names] = YAML.load_file("document.yml")["authors"].each_with_index.map { |a, i| "#{a["name"]} #{a["surname"]}" }
  authors[:emails] = YAML.load_file("document.yml")["authors"].each_with_index.map { |a, i| a["email"] }

  opts[:dest] ||= Pathname(source).sub_ext(".pdf").basename.to_s
  opts[:backend] ||= Pathname.new(opts[:dest]).extname.to_s.sub ".", ""
  content = File.read source

  lang_url = "https://raw.githubusercontent.com/asciidoctor/asciidoctor/master/data/locale/attributes-#{YAML.load_file("document.yml")["lang"]}.adoc"
  content.sub!(/^(= .*?\n)/, "\\1#{Net::HTTP.get(URI.parse(lang_url))}\n")

  attributes = {
    "doctype" => "article",
    "title-page" => true,
    "media" => "prepress",
    "pdf-themesdir" => "themes",
    "pdf-theme" => "article",
    "author" => authors[:names],
    "toc" => "auto",
  }
  
  Asciidoctor.convert content, backend: opts[:backend], safe: :unsafe, attributes: attributes, to_dir: "out", to_file: opts[:dest], mkdirs: true
end

task :default do
  print "Building main document..."
  $stdout.flush
  AsciiDocPublishingToolbox.build dir: Pathname.new(__FILE__).dirname
  puts "    Done"
end

task :features do
  print "Building 'features'..."
  $stdout.flush
  authors = YAML.load_file("document.yml")["authors"].each_with_index.map { |a, i| a["surname"] }
  build_asciidoc "src/analisi-delle-piattaforme.adoc", dest: "formalms-#{authors.join('-')}.pdf"
  puts "    Done"
end
