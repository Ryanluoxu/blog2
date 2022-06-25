---
title: Java Master - S9.LinkedList
date: 2018-02-01 07:54:57
categories: Java Master Course
tags: [Java, course, note, LinkedList]
---
Notes and Exersice for LinkedList of Completed Java Master Class.
<!--more-->

# Notes
- In LinkedList, each elements are linked with previous element and following element.
- LinkedList is faster when adding or removing elements.
- ArrayList is faster when we want to take value by giving index number

Now we have a list:
	- java
	- python
	- sql
	- c
	- html
	- ruby 
If we want to remove "python".
## For ArrayList
All the elements behind "python" will be relocated with new index.
## For LinkedList
Break reference between python to java and sql. java will refer to sql. the changes are only within that area.

# Exercise
## Requirements
- Three classes: 
	- Song: title, duration
	- Album: title, singer, songList
	- Player(main)
- Process:
	1. Automatically create an albumList including 2 albums, 5 songs for each album.
	2. Print all albums with songs
	3. Ask users to add songs into playList by choosing album then song. Press 0 to finish adding.
	4. Print menu for users: 
		- Quit
		- Skip forward to the next song
		- skip backwards to a previous song.  
		- Replay the current song.
		- List the songs in the playlist
		- Remove the current song from the playlist
- hint: listiterator.remove()

## Codes

### Song.java
```
package javamaster.linkedlist;

public class Song {
	private String title;
	private String duration;
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public String getDuration() {
		return duration;
	}
	public void setDuration(String duration) {
		this.duration = duration;
	}
	public Song(String title, String duration) {
		super();
		this.title = title;
		this.duration = duration;
	}
	@Override
	public String toString() {
		return title + "\t" + duration;
	}
}
```

### Album.java
```
package javamaster.linkedlist;

import java.util.List;

public class Album {
	private String albumTitle;
	private String singer;
	private List<Song> songList;
	
	public Album(String albumTitle, String singer, List<Song> songList) {
		super();
		this.albumTitle = albumTitle;
		this.singer = singer;
		this.songList = songList;
	}
	public String getAlbumTitle() {
		return albumTitle;
	}
	public void setAlbumTitle(String albumTitle) {
		this.albumTitle = albumTitle;
	}
	public String getSinger() {
		return singer;
	}
	public void setSinger(String singer) {
		this.singer = singer;
	}
	public List<Song> getSongList() {
		return songList;
	}
	public void setSongList(List<Song> songList) {
		this.songList = songList;
	}
	@Override
	public String toString() {
		return albumTitle + "\t" + singer;
	}
}
```

### Player.java
```
package javamaster.linkedlist;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.ListIterator;
import java.util.Scanner;

public class Player {
	
	static List<Song> playList = new LinkedList<>();
	static ListIterator<Song> iterator;
	static boolean forward;
	static Song currentSong;
    static Scanner scanner = new Scanner(System.in);
	
	public static void main(String[] args) {
		
		List<Album> albumList = createAlbumList();
		
		printAlbumSong(albumList);
		
		addSongToPlayList(albumList);
		
		// start player
		System.out.println("Starting player..");
		iterator = playList.listIterator();
		if (iterator.hasNext()) {
			currentSong = iterator.next();
			System.out.println("Now is playing >> " + currentSong);
			
			// operate player
			boolean exit = false;
			forward = true;
			System.out.println("");
			
			printMenu();
			while (!exit) {
				String choice = null;
				System.out.print("Please choose : ");
				choice = scanner.next();
				System.out.println("");
				
				switch (choice) {
				case "0":
					System.out.println("See you..");
					exit = true;
					break;
				case "1":
					playNextSong();
					break;
				case "2":
					playLastSong();
					break;
				case "3":
					replay();
					break;
				case "4":
					listSong();
					break;
				case "5":
					removeCurrentSong();
					break;
				case "6":
					printMenu();
					break;
				default:
					break;
				}
			}
		} else {
			System.err.println("No song found in playlist..");
		}
	}

	private static List<Album> createAlbumList() {
		List<Album> albumList = new ArrayList<>();
		List<Song> songList = new ArrayList<>();
		songList.add(new Song("tears in heaven", "3:30"));
		songList.add(new Song("rolling in the deep", "4:30"));
		songList.add(new Song("complicated", "3:50"));
		songList.add(new Song("satisfied", "2:40"));
		songList.add(new Song("faded", "4:10"));
		albumList.add(new Album("The Sea", "Anyone", songList));
		
		songList = new ArrayList<>();
		songList.add(new Song("java", "4:50"));
		songList.add(new Song("python", "2:30"));
		songList.add(new Song("R", "1:50"));
		songList.add(new Song("C++", "5:40"));
		songList.add(new Song("C#", "2:10"));
		albumList.add(new Album("The Sky", "Programmer", songList));
		return albumList;
	}

	private static void addSongToPlayList(List<Album> albumList) {
		int albumChoice = -1;
		int songChoice = -1;
		boolean finishAddSong = false;
		System.out.println("Add songs to playlist:");
		while (!finishAddSong) {
			System.out.print("choose album number (press 0 to finish) : ");
			try {
				albumChoice = scanner.nextInt();
				scanner.nextLine();
				if (albumChoice == 0) {
					finishAddSong = true;
				} else if(albumChoice > 0 && albumChoice <= albumList.size()) {
					List<Song> songs = albumList.get(albumChoice-1).getSongList();
					System.out.print("choose song number: ");
					try {
						songChoice = scanner.nextInt();
						scanner.nextLine();
						if(songChoice > 0 && songChoice <= songs.size()) {
							playList.add(songs.get(songChoice-1));
						} else {
							System.err.println("Invalid choice ..");
						}
					} catch (Exception e) {
						System.out.println(e);
					}
				} else {
					System.err.println("Invalid choice ..");
				}
			} catch (Exception e) {
				System.out.println(e);
			}
		}
	}

	private static void printAlbumSong(List<Album> albumList) {
		ListIterator<Album> albumIterator = albumList.listIterator();
		ListIterator<Song> songIterator;
		System.out.println("You have: ");
		while (albumIterator.hasNext()) {
			Album album = (Album) albumIterator.next();
			System.out.println("Album [" + albumIterator.nextIndex() + "] : " + album);
			songIterator = album.getSongList().listIterator();
			while (songIterator.hasNext()) {
				Song song = (Song) songIterator.next();
				System.out.println("Song [" + songIterator.nextIndex() + "] : " + song);
			}
		}
	}

	private static void removeCurrentSong() {
		if (currentSong != null) {
			System.out.println("Removing >> "  + currentSong);
			iterator.remove();
			if (iterator.hasNext() || iterator.hasPrevious()) {
				if (forward) {
					if (!iterator.hasNext()) {
						iterator.previous();
					}
					playNextSong();
				} else {
					if (!iterator.hasPrevious()) {
						iterator.next();
					}
					playLastSong();
				}
			} else {
				System.out.println("No song in the list..");
				currentSong = null;
			}
		} else {
			System.out.println("No song in the list..");
		}
	}

	private static void listSong() {
		System.out.println("--- Play List ---");
		for (Song song : playList) {
			System.out.println(song);
		}
		System.out.println("");
	}

	private static void replay() {
		System.out.println("Now is playing >> " + currentSong);
	}

	private static void playLastSong() {
		if (!forward) {
			if (iterator.hasPrevious()) {
				currentSong = iterator.previous();
				System.out.println("Now is playing >> " + currentSong);
			} else {
				System.out.println("You are at the start of list..");
			}
		} else {
			if (iterator.hasPrevious()) {
				iterator.previous();
				if (iterator.hasPrevious()) {
					currentSong = iterator.previous();
					System.out.println("Now is playing >> " + currentSong);
					forward = false;
				} else {
					System.out.println("You are at the start of list..");
				}
			} else {
				System.out.println("You are at the start of list..");
			}
		}
	}

	private static void playNextSong() {
		if (forward) {
			if (iterator.hasNext()) {
				currentSong = iterator.next();
				System.out.println("Now is playing >> " + currentSong);
			} else {
				System.out.println("You are at the end of list..");
			}
		} else {
			if (iterator.hasNext()) {
				iterator.next();
				if (iterator.hasNext()) {
					currentSong = iterator.next();
					System.out.println("Now is playing >> " + currentSong);
					forward = true;
				} else {
					System.out.println("You are at the end of list..");
				}
			} else {
				System.out.println("You are at the end of list..");
			}
		}
	}

	private static void printMenu() {
		System.out.println("--- Player Menu  ---");
		System.out.println("0. Quit");
		System.out.println("1. Skip forward");
		System.out.println("2. Skip backfard");
		System.out.println("3. Replay");
		System.out.println("4. List songs");
		System.out.println("5. Remove current song");
		System.out.println("6. Show menu");
		System.out.println("");
	}
}
```