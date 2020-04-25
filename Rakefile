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
    "sectnums" => true,
    "sectnumsdepth" => true,
    "xrefstyle" => "full",
    "section-refsig" => "Sezione",
  }
  
  Asciidoctor.convert content, backend: opts[:backend], safe: :unsafe, attributes: attributes, to_dir: "docs", to_file: opts[:dest], mkdirs: true
end

def build_phase(phase_name, input, output)
  print "Building '#{phase_name}'..."
  $stdout.flush
  authors = YAML.load_file("document.yml")["authors"].each_with_index.map { |a, i| a["surname"] }
  build_asciidoc "src/#{input}.adoc", dest: "#{output}.pdf"
  puts "\r✓ Builded '#{phase_name}'   "
end

task :default => %w(features ambiente) do
  print "Building main document..."
  $stdout.flush
  AsciiDocPublishingToolbox.build dir: Pathname.new(__FILE__).dirname
  puts "\r✓ Builded main document   "
end

task :features do
  build_phase 'features', 'analisi-delle-piattaforme', 'formalms-fsc'
end

task :ambiente do
  build_phase 'ambiente', 'progettazione-ambiente', 'progettazione-ambiente-fsc'
end
