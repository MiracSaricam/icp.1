import List "mo:base/List";
import Trie "mo:base/Trie";
import Nat32 "mo:base/Nat32";
import Option "mo:base/Option";




actor CinemaSystem {


  public type HallId = Nat32;
  public type FilmId = Nat32;
  public type SeatNumber = Nat32;

  public type Film = {
    title: Text;
    genre: Text;
    ageLimit: Nat32;
    duration: Nat32; 
  };

  public type Hall = {
    name: Text;
    seatCount: Nat32; 
  };

  public type Session = {
    filmId: FilmId;
    hallId: HallId;
    time: Text; 
  };


  private stable var nextHallId: HallId = 0;
  private stable var nextFilmId: FilmId = 0;

  private stable var halls: Trie.Trie<HallId, Hall> = Trie.empty(); 
  private stable var films: Trie.Trie<FilmId, Film> = Trie.empty(); 
  private stable var sessions: List.List<Session> = List.nil(); 

  
  public func createHall(hall: Hall): async HallId {
    let hallId = nextHallId;
    nextHallId += 1;
    halls := Trie.replace(halls, key(hallId), Nat32.equal, ?hall).0;
    return hallId;
  };

  public func createFilm(film: Film): async FilmId {
    let filmId = nextFilmId;
    nextFilmId += 1;
    films := Trie.replace(films, key(filmId), Nat32.equal, ?film).0;
    return filmId;
  };

  public query func readHall(hallId: HallId): async ?Hall {
    return Trie.find(halls, key(hallId), Nat32.equal);
  };

  public query func readFilm(filmId: FilmId): async ?Film {
    return Trie.find(films, key(filmId), Nat32.equal);
  };

  public func updateHall(hallId: HallId, hall: Hall): async Bool {
    let exists = Option.isSome(Trie.find(halls, key(hallId), Nat32.equal));
    if (exists) {
      halls := Trie.replace(halls, key(hallId), Nat32.equal, ?hall).0;
    };
    return exists;
  };

  public func updateFilm(filmId: FilmId, film: Film): async Bool {
    let exists = Option.isSome(Trie.find(films, key(filmId), Nat32.equal));
    if (exists) {
      films := Trie.replace(films, key(filmId), Nat32.equal, ?film).0;
    };
    return exists;
  };

  public func deleteHall(hallId: HallId): async Bool {
    let exists = Option.isSome(Trie.find(halls, key(hallId), Nat32.equal));
    if (exists) {
      halls := Trie.remove(halls, key(hallId), Nat32.equal).0;
    };
    return exists;
  };

  public func deleteFilm(filmId: FilmId): async Bool {
    let exists = Option.isSome(Trie.find(films, key(filmId), Nat32.equal));
    if (exists) {
      films := Trie.remove(films, key(filmId), Nat32.equal).0;
    };
    return exists;
  };

  
  public func createSession(session: Session): async () {
    
    sessions := List.push(session, sessions);  
    
  };

  public query func listSessions(): async List.List<Session> {
    return sessions;
  };

  private func key(x: Nat32): Trie.Key<Nat32> {
    return { key = x; hash = x }; 
  };
}
