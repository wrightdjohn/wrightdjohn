package com.jpmchase.sealdataextract.common;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ObjectNode;

import java.io.File;
import java.nio.file.Files;
import java.util.Iterator;

public class DailyLeaderReport {
   ObjectMapper objectMapper = new ObjectMapper();

   public static void main(String[] args) throws Exception {
       DailyLeaderReport report = new DailyLeaderReport();
       String[] people = {"Nick Riordan", "Samuel Singh", "John D. Wright", "Paul Bender", "Sandeep Lakka", "fred orispaa",
                        "Matthew Chaddock", "joseph gladson", "Aditya Yajaman", "Brad Roy", "sruthireddy65", "Alasdair Rae",
                        "Daniel Abela", "FloraQinQvQ", "yousufnzm", "Karow Maruf", "Fabien Duranti", "James MacKenzie",
                        "Sopuruchi Femi-Olekanma", "Cyril Chevalley", "Deepa Prasanth", "Yue Shi", "Deekshith Bommidi", "Laurence Watts",
                        "cstpalash", "Scott Stevens", "Reuben Taylor", "shaunnorris", "Christopher Neylan", "Aruna Ponaka",
                        "Jitesh Yadav"};

       for (String person:people) {
           report.runFullRank(person);
       }
   }

   public void runFullRank(String name) throws Exception {
       int sum = 0;
       int days = 7;
       System.out.print(name+"\t");
       for (int i=1; i<=days; ++i) {
           int rank1 = rank(i, 1, name);
           System.out.print((rank1)+"\t");
           int rank2 = rank(i, 2, name);
           System.out.print((rank2)+"\t");
           System.out.print((rank1+rank2)+"\t");
           sum = sum+rank1+rank2;
       }

       System.out.println(""+sum);
   }

   public int rank(int day, int problem, String namePart) throws Exception {
        File file = new File("/home/v646425/jdw/aoc.txt");
        String buff = Files.readString(file.toPath());
        JsonNode jo = objectMapper.readTree(buff);
        ObjectNode root = (ObjectNode) jo.get("members");
        Iterator<String> it = root.fieldNames();
        int playerScore = 0;
        while (it.hasNext()) {
            JsonNode player = root.get(it.next());
            if (player instanceof ObjectNode) {
                String name = player.get("name").asText();
                if (name == null) {
                    continue;
                }
                else
                if (name.contains(namePart)) {
                    ObjectNode dayNode = (ObjectNode) player.get("completion_day_level").get("" + day);
                    if (dayNode != null) {
                        ObjectNode q2Node = (ObjectNode) dayNode.get("" + problem);
                        if (q2Node != null) {
                            playerScore = q2Node.get("get_star_ts").asInt();
                        }
                    }
                }
            }
        }

       int beatOthers = 0;
       int total = 0;
       it = root.fieldNames();
       while (it.hasNext()) {
           JsonNode player = root.get(it.next());
           if (player instanceof ObjectNode) {
               String name = player.get("name").asText();
               if (name == null) {
                   beatOthers++;
                   total++;
                   continue;
               }
               ObjectNode dayNode = (ObjectNode) player.get("completion_day_level").get(""+day);
               if (dayNode == null) {
                   beatOthers++;
                   total++;
                   continue;
               }
               ObjectNode q2Node = (ObjectNode) dayNode.get(""+problem);
               if (q2Node ==  null) {
                   beatOthers++;
                   total++;
                   continue;
               }
               if (playerScore <= q2Node.get("get_star_ts").asInt()) {
                   beatOthers++;
               }
               total++;
           }
       }
       //System.out.println(namePart+" --> beat "+johnBeat+" out of a total of "+total+" on day "+day+" problem #"+problem);
       return beatOthers;
    }


    public void report(int day) throws Exception {
        File file = new File("/home/v646425/jdw/aoc.txt");
        String buff = Files.readString(file.toPath());
        JsonNode jo = objectMapper.readTree(buff);
        ObjectNode root = (ObjectNode) jo.get("members");
        Iterator<String> it = root.fieldNames();
        while (it.hasNext()) {
            JsonNode player = root.get(it.next());
            if (player instanceof ObjectNode) {
                String name = player.get("name").asText();
                if (name == null) {
                    continue;
                }
                ObjectNode dayNode = (ObjectNode) player.get("completion_day_level").get(""+day);
                if (dayNode == null) {
                    continue;
                }
                ObjectNode q2Node = (ObjectNode) dayNode.get("2");
                if (q2Node ==  null) {
                    continue;
                }
                System.out.print(name+" ");
                System.out.println(q2Node.get("get_star_ts").asText());
            }
        }
    }
}

