// @Bek Brace [ Twitter - Dev.to - GitHub ]
// bekbrace.com - info@bekbrace.com
// Source Code : Michael Hermann [ mjheaO ]

<!--<template>
  <div class="container">
    <p>games library</p>
  </div>
</template> -->

<template>
<div class="jumbotron vertical-center">
  <div class="container">
    <!-- Bootswatch -->
   <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@4.5.2/dist/sketchy/bootstrap.min.css" integrity="sha384-RxqHG2ilm4r6aFRpGmBbGTjsqwfqHOKy1ArsMhHusnRO47jcGqpIQqlQK/kmGy9R" crossorigin="anonymous">
 <!-- <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@4.5.2/dist/superhero/bootstrap.min.css" integrity="sha384-HnTY+mLT0stQlOwD3wcAzSVAZbrBp141qwfR4WfTqVQKSgmcgzk+oP0ieIyrxiFO" crossorigin="anonymous"> -->

    <div class="row">
      <div class="col-sm-12 ">
       <h1 class="text-center bg-primary text-white" style="border-radius:10px"> Games Library 🕹️ </h1>
        <hr><br>
        
         <!-- Alert -->
        <b-alert variant="success" v-if="showMessage" show> {{ message }} </b-alert>
        <!-- Add Game button -->
        <button type="button" class="btn btn-success btn-sm" v-b-modal.game-modal>Add Game</button>
        <br><br>
        <!-- Add a bootstrap table -->
        <table class="table table-hover">
          <thead>
            <tr>
              <!-- table header cells -->
              <th scope="col">Title</th>
              <th scope="col">Genre</th>
              <th scope="col">Played ?</th>
              <th scope="col">Actions</th>
            </tr>
          </thead>
          <tbody>
            <!-- tr: table row -->
            <tr v-for="(game, index) in games" :key="index">
              <!-- td : table data -->
              <td>{{game.title}}</td>
              <td>{{game.genre}}</td>
              <td>
                <span v-if="game.played">Yes</span>
                <span v-else>No</span>
              </td>
              <td>
                <div class="btn-group" role="group">
                   <!-- 2 Handle update button click -->
                <button
                  type="button"
                  class="btn btn-info btn-sm"
                  v-b-modal.game-update-modal
                  @click="editGame(game)"> Update </button>
                  <button type="button" class="btn btn-danger btn-sm" @click="deleteGame(game)">Delete</button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
        
        <!-- Footer -->
        <Footer class="bg-primary text-white text-center" style="border-radius:10px">Copyright &copy;. All Rights Reserved 2021.</Footer>

      </div>
    </div>

    <!-- Start of Modal 1 -->
    <b-modal ref="addGameModal"
         id="game-modal"
         title="Add a new game" hide-backdrop
         hide-footer
         >
    <b-form @submit="onSubmit" @reset="onReset" class="w-100">
    <b-form-group id="form-title-group"
                  label="Title:"
                  label-for="form-title-input">
      <b-form-input id="form-title-input"
                    type="text"
                    v-model="addGameForm.title"
                    required
                    placeholder="Enter title">
      </b-form-input>
    </b-form-group>

    <b-form-group id="form-genre-group"
                  label="Genre:"
                  label-for="form-genre-input">
          <b-form-input id="form-genre-input"
                        type="text"
                        v-model="addGameForm.genre"
                        required
                        placeholder="Enter genre">
        </b-form-input>
      </b-form-group>
      
      <b-form-group id="form-played-group">
            <b-form-checkbox-group v-model="addGameForm.played" id="form-checks">
            <b-form-checkbox value="true">Played?</b-form-checkbox>
        </b-form-checkbox-group>
      </b-form-group>

      <b-button type="submit" variant="outline-info">Submit</b-button>
      <b-button type="reset" variant="outline-danger">Reset</b-button>
    </b-form>
  </b-modal>
  <!-- End of modal 1 -->


  <!-- Start of Modal 2 -->
  <b-modal ref="editGameModal"
         id="game-update-modal"
         title="Update" hide-backdrop
         hide-footer>
  <b-form @submit="onSubmitUpdate" @reset="onResetUpdate" class="w-100">
    
  <b-form-group id="form-title-edit-group"
                label="Title:"
                label-for="form-title-edit-input">
      <b-form-input id="form-title-edit-input"
                    type="text"
                    v-model="editForm.title"
                    required
                    placeholder="Enter title">
      </b-form-input>
    </b-form-group>

    <b-form-group id="form-genre-edit-group"
                  label="Genre:"
                  label-for="form-genre-edit-input">
        <b-form-input id="form-genre-edit-input"
                      type="text"
                      v-model="editForm.genre"
                      required
                      placeholder="Enter genre">
        </b-form-input>
      </b-form-group>
      
    <b-form-group id="form-played-edit-group">
      <b-form-checkbox-group v-model="editForm.played" id="form-checks">
        <b-form-checkbox value="true">Played?</b-form-checkbox>
      </b-form-checkbox-group>
    </b-form-group>

    <b-button-group>
      <b-button type="submit" variant="outline-info">Update</b-button>
      <b-button type="reset" variant="outline-danger">Cancel</b-button>
    </b-button-group>
  </b-form>
</b-modal>
<!-- end of modal 2 -->
  </div>
  </div>
</template>



<script>
import axios from 'axios';

export default {
  data() {
    return {
      games: [],
      addGameForm: {
        title: '',
        genre: '',
        played: [],
      },
      editForm: {
      id: '',
      title: '',
      genre: '',
      played: [],
      },
    };
  },

  message:'',

methods: {
    // 1 GET METHOD
    getGames() {
      const path = 'http://localhost:5000/games';
      axios.get(path)
        .then((res) => {
          this.games = res.data.games;
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // 2 Add Game Button
    addGame(payload) {
      const path = 'http://localhost:5000/games';
      axios.post(path, payload)
        .then(() => {
          this.getGames();
          
          // for message alert
          this.message = 'Game added 🕹️ !';
          
          // to show message when game is added
          this.showMessage = true;
  
        })
        .catch((error) => {
          console.log(error);
          this.getGames();
        });
    },

     // 5 initForm - add ediForm after the update method
     initForm() {
        this.addGameForm.title = '';
        this.addGameForm.genre = '';
        this.addGameForm.played = [];
        this.editForm.id = '';
        this.editForm.title = '';
        this.editForm.genre = '';
        this.editForm.played = [];
      }, 

    // 3 Submit form validator in the template @submit="onSubmit"  
    onSubmit(e) {
      e.preventDefault();
      this.$refs.addGameModal.hide();
      let played = false;
      if (this.addGameForm.played[0]) played = true;
      const payload = {
        title: this.addGameForm.title,
        genre: this.addGameForm.genre,
        played, 
      };
      this.addGame(payload);
      this.initForm();
    },

    
  // MODAL 2
  // a- Handle the form Submit after updating
    onSubmitUpdate(e) {
    e.preventDefault();
    this.$refs.editGameModal.hide();
    let played = false;
    if (this.editForm.played[0]) played = true;
    const payload = {
      title: this.editForm.title,
      genre: this.editForm.genre,
      played,
    };
    this.updateGame(payload, this.editForm.id);
  },

  // b- On reset method to reset items to default values
    onReset(e) {
      e.preventDefault();
      this.$refs.addGameModal.hide();
      this.initForm();
    },

// 4 Update Alert Message 
// Once the update is effective, we will get a message telling us that Game Updated, and display the list of games after the update
updateGame(payload, gameID) {
  const path = `http://localhost:5000/games/${gameID}`;
  axios.put(path, payload)    
    .then(() => {
      this.getGames();
      this.message = 'Game updated ⚙️!';
      this.showMessage =  true;
    })
    .catch((error) => {
      console.error(error);
      this.getGames();
    });
},

 // Handle Update Button 
editGame(game) {
  this.editForm = game;
},

// 5 Handle reset / cancel button click
onResetUpdate(e) {
  e.preventDefault();
  this.$refs.editGameModal.hide();
  this.initForm();
  this.getGames(); 
},

// Remove Game [ Delete Button ]
removeGame(gameID) {
  const path = `http://localhost:5000/games/${gameID}`;
  axios.delete(path)
    .then(() => {
      this.getGames();
      this.message = 'Game Removed 🗑️!';
      this.showMessage = true;
    })
    .catch((error) => {
      // eslint-disable-next-line
      console.error(error);
      this.getGames();
    });
},
// Handle Delete Button
deleteGame(game) {
  this.removeGame(game.id);
},

  },
  created() {
    this.getGames(); 
  },
};
</script>