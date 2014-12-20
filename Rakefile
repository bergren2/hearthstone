require 'middleman-gh-pages'
require 'open-uri'
require 'json'

raw_filepath = File.expand_path("./../data/cards_raw.json", __FILE__)
dict_filepath = File.expand_path("./../data/cards_dictionary.json", __FILE__)
collection_filepath = File.expand_path("./../data/collection.json", __FILE__)

def ask_user
  STDIN.gets.strip
end

def ask_card_name dict
  loop do
    print "Which card would you like to modify in your collection? "
    name = ask_user
    return dict[name] if dict.assoc(name)
    print "Couldn't find the card '#{name}'.\n"
  end
end

namespace :cards do
  desc "Updates cards data from hearthstonejson.com"
  task :update do
    print "Updating cards_raw.json... "
    url = "http://hearthstonejson.com/json/AllSets.json"

    open(raw_filepath, "w") do |f|
      f << open(url).read
    end

    print "Done.\n"
    Rake::Task["cards:dict"].invoke
  end

  desc "Creates dictionary of cards based on name"
  task :dict do
    print "Updating cards_dictionary.json... "
    infile = open(raw_filepath, "r")
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

    open(dict_filepath, "w") do |f|
      f << working_hash.to_json
    end
    print "Done.\n"
  end
end

namespace :collection do
  desc "add or remove cards from collection"
  task :edit do
    print "Loading dictionary... "
    infile = open(dict_filepath)
    dict = JSON.parse infile.read
    infile.close
    print "Done.\n"

    print "Loading collection... "
    infile = open(collection_filepath)
    collection = JSON.parse infile.read
    infile.close
    print "Done.\n"

    loop do
      card = ask_card_name dict
      id = card["id"]
      card_owned = collection[id] # nil if not owned

      if card_owned
        print "You have #{card_owned[0]} regular card(s) and #{card_owned[1]} golden " +
              "cards currently in your collection.\n"
      else
        print "You don't have that card yet.\n"
        collection[id] = [0, 0]
      end

      print "How many regular cards do you have? "
      amt_reg = ask_user.to_i
      print "How many golden cards do you have? "
      amt_gold = ask_user.to_i

      collection[id] = [amt_reg, amt_gold]

      print "Add another card? (y/n) "
      yes_or_no = ask_user.downcase
      break if yes_or_no == 'n'
    end

    open(collection_filepath, "w") do |f|
      f << JSON.pretty_generate(collection)
    end
  end
end