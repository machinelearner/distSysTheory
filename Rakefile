desc "builds the .pdf document"
task :pdf do
  file_name = ENV['FILE'] || 'konspekt'
  system("pdflatex #{file_name}")
  system("bibtex #{file_name}")
  2.times { system("pdflatex #{file_name}") }
end

desc "removes generated files"
task :clean do
  system "rm -rf *.aux *.log *.pdf *.bbl *.blg"
end

def check_entry(entry_key)
  puts "ENTRY argument is required!" unless entry_key
  return entry_key
end

def load_bibtex_gem
  return require 'bibtex_parser'
rescue LoadError
  return puts "gem rbibtex is not installed!"
end

def bibliography_entries
  BibTeX::Parser.parse_bibtex_file("bibliografia.bib")
end

def get_bib_entry_by_key(entry_key)
  entry = bibliography_entries.detect {|e| e.key == entry_key}
  puts "no such entry: #{entry_key}" unless entry
  return entry
end

def check_url(entry)
  puts "entry #{entry_key} has no url!" unless entry.url
  return entry.url
end

def make_directory_unless_exists(name)
  Dir.mkdir(name) unless File.exist?(name)
end

def root_files_dir
  'bibliografia_pliki'
end

def entry_dir_for_key(entry_key)
  File.join(root_files_dir, entry_key)
end

def make_entry_dir(entry_key)
  make_directory_unless_exists(root_files_dir)
  entry_dir = entry_dir_for_key(entry_key)
  make_directory_unless_exists(entry_dir)
  return entry_dir
end

def download_entry(entry_key)
  return unless check_entry(entry_key)
  return unless load_bibtex_gem()
  return unless (entry = get_bib_entry_by_key(entry_key))
  return unless (entry_url = check_url(entry))
  entry_dir = make_entry_dir(entry_key)
  system("wget -P #{entry_dir} -k -p -E -N -nd #{entry_url}")
end

desc "downloads html document specified in the ENTRY parameter"
task :download do
  download_entry(ENV['ENTRY'])
end

def list_not_downloaded(with_url)
  load_bibtex_gem
  bibliography_entries.each do |entry|
    unless File.exist?(entry_dir_for_key(entry.key))
      puts with_url ? "#{entry.key} : #{entry.url}" : entry.key
    end  
  end
end

desc "lists all bibliografy entries that were not downloaded (add SHOW_URL to show urls)"
task :not_downloaded do
  list_not_downloaded(!!ENV['SHOW_URL'])
end