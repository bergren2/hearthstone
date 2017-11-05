# for editing the collection
require "mini_magick"
require "open-uri"
require "json"

# for tests
require "jasmine"
load "jasmine/tasks/jasmine.rake"

@raw_filepath = File.expand_path("./../data/cards_raw.json", __FILE__)
@dict_filepath = File.expand_path("./../data/cards_dictionary.json", __FILE__)
@collection_filepath = File.expand_path("./../data/collection.json", __FILE__)
@data_points = ["id", "text", "type", "rarity", "cost", "attack", "health", "race", "mechanics"]
@spinner = {
  index: 0,
  chars: ["/", "-", "\\"]
}

def spinner
  i = (@spinner[:index] + 1) % @spinner[:chars].size
  @spinner[:index] = i
  @spinner[:chars][i]
end

def load_collection
  print "Loading collection... "
  infile = open(@collection_filepath)
  collection = JSON.parse infile.read
  infile.close
  print "Done.\n"
  return collection
end

def write_collection hash
  print "Writing collection... "
  open(@collection_filepath, "w") do |f|
    f << JSON.pretty_generate(hash)
  end
  print "Done.\n"
end

def load_dictionary
  print "Loading dictionary... "
  infile = open(@dict_filepath)
  dict = JSON.parse infile.read
  infile.close
  print "Done.\n"
  return dict
end

def ask_user
  STDIN.gets.strip
end

def ask_card_name dict
  loop do
    print "Which card would you like to modify in your collection? "
    name = ask_user
    return [name, dict[name]] if dict.assoc(name)
    print "Couldn't find the card '#{name}'.\n"
  end
end

def download_and_crop_image filename
  url = "http://wow.zamimg.com/images/hearthstone/cards/enus/original/#{filename}"
  path = File.expand_path("./../source/images/cards/#{filename}", __FILE__)
  open(url) do |image|
    File.open(path, "w") do |file|
      file.puts image.read
    end
  end

  image = MiniMagick::Image.open(path)
  image.crop("100x100+100+120") #hardcodez ftw
  image.write path
end

namespace :cards do
  desc "Updates cards data from hearthstonejson.com"
  task :update do
    print "Updating cards_raw.json... "
    url = "https://hearthstonejson.com/json/AllSets.json"

    open(@raw_filepath, "w") do |f|
      f << open(url).read
    end

    print "Done.\n"
    Rake::Task["cards:dict"].invoke
  end

  desc "Creates dictionary of cards based on name"
  task :dict do
    print "Updating cards_dictionary.json... "
    infile = open(@raw_filepath, "r")
    raw_hash = JSON.parse infile.read
    infile.close

    working_hash = {}
    raw_hash.each do |set_name, set|
      set.each do |card|
        if card["collectible"] and card["type"] != "Hero"
          name = card.delete "name"
          working_hash[name] = card # NOTE: dangerous
        end
      end
    end

    open(@dict_filepath, "w") do |f|
      f << working_hash.to_json
    end
    print "Done.\n"
  end

  desc "Download collection cards from Hearthhead"
  task :grab_images do
    collection = load_collection

    images = Dir.entries(File.expand_path("./../source/images/cards", __FILE__))

    collection.each do |name, data|
      id = data["id"]
      print "\rDownloading collection card images... #{spinner}"

      if data["amount"][0] + data["amount"][1] > 0
        filename = "#{id}.png"
        download_and_crop_image(filename) unless images.include? filename
      end
    end

    print "\rDownloading collection card images... Done.\n"
  end
end

namespace :collection do
  desc "add or remove cards from collection"
  task :edit do
    dict = load_dictionary
    collection = load_collection

    loop do
      card = ask_card_name dict
      name = card[0]

      if collection[name]
        card_owned = collection[name]["amount"]
        print "You have #{card_owned[0]} regular card(s) and #{card_owned[1]} golden " +
              "cards currently in your collection.\n"
      else
        print "You don't have that card yet.\n"
        collection[name] = {}
        collection[name]["amount"] = [0, 0]
        @data_points.each do |pt|
          collection[name][pt] = card[1][pt]
        end
      end

      print "How many regular cards do you have? "
      amt_reg = ask_user.to_i
      print "How many golden cards do you have? "
      amt_gold = ask_user.to_i

      collection[name]["amount"] = [amt_reg, amt_gold]

      print "Add another card? (y/n) "
      yes_or_no = ask_user.downcase
      break if yes_or_no == 'n'
    end

    write_collection collection

    Rake::Task["cards:grab_images"].invoke
  end

  desc "Rebuilds collection with new data points"
  task :rebuild do
    collection = load_collection
    dict = load_dictionary

    print "Rebuilding collection... "
    collection.each do |name, data|
      @data_points.each do |pt|
        unless data.has_key? pt
          data[pt] = dict[name][pt]
        end
      end
    end
    print "Done\n"

    write_collection collection
  end
end

task :travis do
  ["rake jasmine:ci"].each do |cmd|
    puts "Starting to run #{cmd}..."
    system("export DISPLAY=:99.0 && bundle exec #{cmd}")
    raise "#{cmd} failed!" unless $?.exitstatus == 0
  end
end

task default: [:travis]
