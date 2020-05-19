require "pathname"
require "asciidoctor"
require "asciidoctor-diagram"
require "asciidoctor-pdf"
require "asciidoc_publishing_toolbox"
require "yaml"
require "net/http"
require "fileutils"

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
    "pdf-fontsdir" => "GEM_FONTS_DIR,themes/fonts/",
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

task :default => %w(clean features ambiente manuali main contenuti)

task :clean do
  FileUtils.rm_rf Pathname.new(__FILE__).dirname + 'docs/'
end

task :main do
  print "Building main document..."
  $stdout.flush
  Dir.chdir Pathname.new(__FILE__).dirname
  AsciiDocPublishingToolbox.build dir: Pathname.new(__FILE__).dirname
  puts "\r✓ Builded main document   "
end

task :manuali do
  %w(studente docente).each do |type|
    print "Building guide for '#{type}'..."
    $stdout.flush
    Dir.chdir Pathname.new(__FILE__).dirname + "manuali/#{type}"
    AsciiDocPublishingToolbox.build dir: Pathname.new(__FILE__).dirname + "manuali/#{type}"
    FileUtils.mkdir_p Pathname.new(__FILE__).dirname + "docs/manuali/#{type}"
    FileUtils.mv Dir.glob(Pathname.new(__FILE__).dirname + "manuali/#{type}/docs/*"), Pathname.new(__FILE__).dirname + "docs/manuali/#{type}", force: true
    FileUtils.cp_r Pathname.new(__FILE__).dirname + "manuali/#{type}/images/", Pathname.new(__FILE__).dirname + "docs/manuali/#{type}"
    puts "\r✓ Builded guide for '#{type}'   "
  end
end

task :contenuti do
    doc = Pathname.new(__FILE__).dirname + 'contenuti/document.yml'
    print "Building 'contenuti'..."
    $stdout.flush
    Dir.chdir doc.dirname
    AsciiDocPublishingToolbox.build dir: doc.dirname
    FileUtils.mkdir_p Pathname.new(__FILE__).dirname + 'docs/contenuti/'
    FileUtils.mv Dir[doc.dirname + "docs/*"], Pathname.new(__FILE__).dirname + 'docs/contenuti/', force: true
    FileUtils.cp_r doc.dirname + "images/", Pathname.new(__FILE__).dirname + 'docs/contenuti/'
    puts "\r✓ Builded 'contenuti'   "
end

task :features do
  build_phase 'features', 'analisi-delle-piattaforme', 'formalms-fsc'
end

task :ambiente do
  build_phase 'ambiente', 'progettazione-ambiente', 'progettazione-ambiente-fsc'
end
