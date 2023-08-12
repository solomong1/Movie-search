# Movie-search
it contain  search,delete ,add , release Year and  director  of the movie 
import java.util.Iterator;
import java.util.ArrayList;
public class MovieCollection
{
    private ArrayList<Movie> movies;

    public MovieCollection()
    {
        this.movies = fetchMovies("myvideos.txt");
    }

    /**
     * a method for setting the arraylist of movies
     * @param  ArrayList of Movies 
     */
    public void setMovies(ArrayList<Movie> movies)
    {
        this.movies = movies;
    }

    /**
     * a method for getting the arraylist of movies
     * @return  ArrayList of Movies 
     */
    public ArrayList<Movie> getMovies()
    {
        return this.movies;
    }

    /**
     * a method for displaying movies' information based on the arraylist
     * @param  ArrayList of Movies 
     */
    private void listMovies(ArrayList<Movie> movies)
    {

        int number;
        number = 0;
        for(Movie movie: movies)
        {
            System.out.println("Movie#"+ (number+1));
            movie.display();
            number++;
        }

    }

    /**
     *a method for fetching movies' information from databases
     *@params filenames for the text File
     *
     */
    private ArrayList<Movie> fetchMovies(String filename)
    {
        ArrayList<String[]> fileOutput;
        fileOutput = new ArrayList<String[]>();
        FileIO fileio = new FileIO(filename);

        fileOutput = fileio.readFile();
        ArrayList<Movie> movies = new ArrayList<Movie>();

        for(String[]singleOutput: fileOutput)
        {
            String title = singleOutput[0];
            String director = singleOutput[1];
            int rating = Integer.parseInt(singleOutput[5]);
            ArrayList<String> actors = new ArrayList<String>();

            actors.add(singleOutput[2]);
            actors.add(singleOutput[3]);
            actors.add(singleOutput[4]);

            //fileOutput.add(singleOutput);

            movies.add(new Movie(title, director,actors,rating));
        }

        return movies;

    }

    /**
     *a method for WRITING movies' information to databases
     *@params filenames for the text vile
     *
     */
    public void writeMovies(String filename)
    {
        FileIO fileio = new FileIO(filename);
        ArrayList<String[]> outputString = new ArrayList<String[]>();

        for(Movie movie : this.movies)
        {
            String [] singleOutput = new String[6];
            singleOutput[0] = movie.getTitle();
            singleOutput[1] = movie.getDirector();
            singleOutput[2] = movie.getActors().get(0);
            singleOutput[3] = movie.getActors().get(1);
            singleOutput[4] = movie.getActors().get(2);
            singleOutput[5] = String.valueOf(movie.getRating());
            outputString.add(singleOutput);

        }
        fileio.writeFile(outputString);

    }

    /**
     *a method for adding new movie information
     *@params title, director, actors, rating of the movie
     */

    public void addMovie(String title, String director, ArrayList<String> actors, int rating)
    {
        this.movies.add(new Movie(title, director, actors, rating));
    }

    /**
     *a method for showing wether the title is identical with the other or not
     *@params title, director, actors, rating of the movie
     */
    public boolean validateTitle(String title)
    {
        for(Movie movie : this.movies)
        {
            if(title.equalsIgnoreCase(movie.getTitle()))
                return false;

        }

        return true;
    }

    /**
     * a method for displaying movies' information based on the title given
     * 
     */
    public void searchTitle(String title)
    {

        for(Movie movie : this.movies)
        {
            if(searchPartial(title,movie.getTitle()) || title.equalsIgnoreCase("all"))
                movie.display();

        }

    }

    /**
     * a method for chack movie availbility based on the title search
     * @return boolan value indicating availbility
     */
    public boolean searchTitleAvailbility(String keyword)
    {
        for(Movie movie: this.movies)
        {
            if(searchPartial(keyword, movie.getTitle()))
                return true;
        }
        return false;
    }

    /**
     * a method for displaying movies' information based on directors
     * @param the directors arrayList
     */
    public void searchDirectors(ArrayList<String> directors)
    {

        for(Movie movie : this.movies)
        {
            if(matchParam(directors, movie.getDirector()))
                movie.display();

        }

    }

    /**
     * a method for deleting movies based on exact movie title
     * @params keyword which has the exact title
     */
    public void deleteMovies(String keyword)
    {

        Iterator<Movie> iter = movies.iterator();
        boolean available = false;
        while(iter.hasNext())
        {

            Movie currentMovie = iter.next();
            if(currentMovie.getTitle().equalsIgnoreCase(keyword))
            {
                currentMovie.display();
                iter.remove();
                available = true;
            }
        }
        if(available)
        {
            System.out.println("Movie Deleted");
            System.out.println();
            System.out.println("This is the movie now");
            listMovies(this.movies);
        }else
        {
            System.out.println("There is no match");
        }
    }

    /**
     * a method for listing the movie title which has partial word
     * @params keyword which has partial keyword 
     */
    public void partialListMovies(String keyword)
    {

        ArrayList<Movie> tempMovies;

        tempMovies = fetchTempMovie(keyword);

        if(!tempMovies.isEmpty())
        {
            System.out.println("here is the movie to update or delete");
            listMovies(tempMovies);
        }
        else
        {
            System.out.println("There is no match anymore");
        }
    }

    /**
     * a method for update the movie based on the partial search
     * @params keyword which has partial keyword ,option number, new director, new set of actors
     */
    public void updatePartialMovie(String keyword, int option,String director, ArrayList<String>actors)
    {

        ArrayList<Movie> tempMovies;

        tempMovies = fetchTempMovie(keyword);

        updateSingleMovie(option - 1, tempMovies,director, actors);

    }

    /**
     * a method for delete the movie based on the partial search
     * @params keyword which has partial keyword ,option number
     */
    public void deletePartialMovie(String keyword, int option)
    {

        ArrayList<Movie> tempMovies;

        tempMovies = fetchTempMovie(keyword);

        deleteSingleMovie(option - 1, tempMovies);

    }

    /**
     *a method for updating a single movie
     *@params index of the movie to be deleted, temporary Movie list, new director, and new actors
     */
    private void updateSingleMovie(int index, ArrayList<Movie> tempMovies, String director, ArrayList<String>actors)
    {
        Movie updatedMovie = tempMovies.get(index);
        int mainIndex = this.movies.indexOf(updatedMovie);
        this.movies.get(mainIndex).setDirector(director);
        this.movies.get(mainIndex).setActors(actors);

    }

    /**
     *a method for deleting a single movie
     *@params index of the movie to be deleted, temporary movie list
     */
    private void deleteSingleMovie(int index, ArrayList<Movie> tempMovies)
    {
        Movie deletedMovie = tempMovies.get(index);
        this.movies.remove(this.movies.indexOf(deletedMovie));
        tempMovies.remove(deletedMovie);

        System.out.println("Movie Deleted:");
        deletedMovie.display();
        System.out.println();
        System.out.println("This is the movie now:");
        listMovies(this.movies);

    }

    /**
     *a method for checking availbility based on the keyword
     *@params keyword of the movie to check
     */
    public boolean checkAvailbility(String keyword)
    {
        for(Movie movie: this.movies)
        {
            if(searchPartial(keyword, movie.getTitle()))
                return true;
        }
        return false;
    }

    /**
     *a method for getting the size of the movie matching with keyword
     *@params keyword of the movie to check
     */
    public int fetchSize(String keyword)
    {
        ArrayList<Movie> tempMovies;
        tempMovies = new ArrayList<Movie>();
        int size;
        tempMovies = fetchTempMovie(keyword);
        size = tempMovies.size();

        return size;
    }

    /**
     *a method for getting temporary Movie
     *@params keyword of the movies
     */
    private ArrayList<Movie> fetchTempMovie(String keyword)
    {
        ArrayList<Movie> tempMovies;
        tempMovies = new ArrayList<Movie>();

        for(Movie movie: this.movies)
        {
            if(searchPartial(keyword, movie.getTitle()))
                tempMovies.add(movie);
        }

        return tempMovies;
    }

    /**
     *a method for displaying favorite movies
     *@param minimum rating of the movie to be displayed
     */
    public void displayFavorites(int rating)
    {

        ArrayList<Movie> tempMovies = new ArrayList<Movie>();
        for(Movie movie : this.movies)
        {
            if(movie.getRating() >= rating)
            {
                tempMovies.add(movie);
            }
        }
        if(!tempMovies.isEmpty())
        {
            System.out.println("Here is some of the matches");
            listMovies(tempMovies);
        }
        else
        {
            System.out.println("There is no matches");
        }

    }

    /**
     *a method for search a partial match between word and target
     *@params keyword from the user input and target string to be matched
     *@return true if the keyword matches
     */
    private boolean searchPartial(String keyword, String target)
    {

        if(keyword.length() > target.length())
            return false;

        String [] targetArray;
        targetArray = target.split(" ");

        int endIndex;
        endIndex = target.length() - 1;
        int startIndex;
        startIndex = 0;
        int currentIndex;
        currentIndex = keyword.length() - 1;

        while(currentIndex <= endIndex)
        {

            if(startIndex == 0 || Character.isWhitespace(target.charAt(startIndex - 1))){
                String partialString;
                partialString = target.substring(startIndex, currentIndex+1);

                if(keyword.equalsIgnoreCase(partialString))
                    return true;
            }

            startIndex++;
            currentIndex++;
        }

        return false;
    }

    /**
     *a method for search a match between parametre and target
     *@param the parameter in a form of arraylis and the target to be matched
     *@return true if one of the params matches
     */
    public boolean matchParam(ArrayList<String> params, String target)
    {
        for(String singleParam : params)
        {
            if(singleParam.equalsIgnoreCase(target))
                return true;
        }
        return false;
    }
}
