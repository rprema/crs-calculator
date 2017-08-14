<template>
  <div class="crs-form">
      <h1>Canada PR Points Calculator</h1>
      <el-row :gutter="30">
        <el-col :sm="24" :lg="12">
          <el-collapse v-model="activeForms">
      <el-collapse-item name="core">
        <template slot="title">
          Core / human capital
        </template>
        <el-form ref="form" :model="forms.core" :label-position="labelPosition" label-width="220px">
          <el-form-item label="Marital Status">
            <el-radio-group v-model="forms.core.maritalStatus">
              <el-radio :label="'s'">Single</el-radio>
              <el-radio :label="'m'">Married</el-radio>
            </el-radio-group>
          </el-form-item>
          
          <el-form-item label="Age" placeholder="Select">
            <el-select v-model="forms.core.age">
              <el-option
                v-for="item in getAgeOpts()"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.age}}</div>
          </el-form-item>

          <el-form-item label="Education" placeholder="Select">
            <el-select v-model="forms.core.education">
              <el-option
                v-for="item in getEducationOpts()"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.education}}</div>
          </el-form-item>

          <hr/>

          <el-form-item label="First Language Scores">
            <el-radio-group v-model="forms.core.firstLang" v-on:change="resetSecondLang">
              <el-radio label="IELTS"></el-radio>
              <el-radio label="CELPIP"></el-radio>
              <el-radio label="TEF"></el-radio>
            </el-radio-group>
            <div class="score-badge">{{factors.firstLang}}</div>
          </el-form-item>
        
          <el-form-item label="Speaking">
            <el-select v-model="forms.core.firstLangSpeaking">
              <el-option
                v-for="item in getLangOpts('speaking', forms.core.firstLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.firstLangSpeaking}}</div>
          </el-form-item>
        
          <el-form-item label="Listening">
            <el-select v-model="forms.core.firstLangListening">
              <el-option
                v-for="item in getLangOpts('listening', forms.core.firstLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.firstLangListening}}</div>
          </el-form-item>
        
          <el-form-item label="Reading">
            <el-select v-model="forms.core.firstLangReading">
              <el-option
                v-for="item in getLangOpts('reading', forms.core.firstLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.firstLangReading}}</div>
          </el-form-item>
        
          <el-form-item label="Writing">
            <el-select v-model="forms.core.firstLangWriting">
              <el-option
                v-for="item in getLangOpts('writing', forms.core.firstLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.firstLangWriting}}</div>
          </el-form-item>

          <el-form-item label="Enable Second Language">
            <el-switch v-model="forms.core.enableSecondLang"></el-switch>
          </el-form-item>

          <el-form-item label="Second Language Scores" v-if="forms.core.enableSecondLang" v-on:change="resetSecondLang">
            <el-radio-group v-model="forms.core.secondLang">
              <el-radio :label="'none'">None</el-radio>
              <el-radio label="IELTS" v-if="forms.core.firstLang === 'TEF'"></el-radio>
              <el-radio label="CELPIP" v-if="forms.core.firstLang === 'TEF'"></el-radio>
              <el-radio label="TEF" v-if="forms.core.firstLang !== 'TEF'"></el-radio>
            </el-radio-group>
            <div class="score-badge">{{factors.secondLang}}</div>
          </el-form-item>

          <el-form-item label="Speaking" v-if="forms.core.enableSecondLang">
            <el-select v-model="forms.core.secondLangSpeaking" v-bind:disabled="updateSecondLang()">
              <el-option
                v-for="item in getLangOpts('speaking', forms.core.secondLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.secondLangSpeaking}}</div>
          </el-form-item>
        
          <el-form-item label="Listening" v-if="forms.core.enableSecondLang">
            <el-select v-model="forms.core.secondLangListening" v-bind:disabled="updateSecondLang()">
              <el-option
                v-for="item in getLangOpts('listening', forms.core.secondLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.secondLangListening}}</div>
          </el-form-item>
        
          <el-form-item label="Reading" v-if="forms.core.enableSecondLang">
            <el-select v-model="forms.core.secondLangReading" v-bind:disabled="updateSecondLang()">
              <el-option
                v-for="item in getLangOpts('reading', forms.core.secondLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.secondLangReading}}</div>
          </el-form-item>
        
          <el-form-item label="Writing" v-if="forms.core.enableSecondLang">
            <el-select v-model="forms.core.secondLangWriting" v-bind:disabled="updateSecondLang()">
              <el-option
                v-for="item in getLangOpts('writing', forms.core.secondLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.secondLangWriting}}</div>
          </el-form-item>

          <hr/>

          <el-form-item label="Canada Work Experience" placeholder="Select">
            <el-select v-model="forms.core.canadaWorkExp">
              <el-option
                v-for="item in getExperienceOpts(5)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.canadaWorkExp}}</div>
          </el-form-item>

          <el-form-item label="Foreign Work Experience">
            <el-select v-model="forms.core.foreignWorkExp">
              <el-option
                v-for="item in getExperienceOpts(2)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.foreignWorkExp}}</div>
          </el-form-item>

        </el-form>
      </el-collapse-item>

      <!-- Additional Points -->
      <el-collapse-item name="additional">
        <template slot="title">
          Additional points
        </template>

        <el-form ref="form" :model="forms.additional" :label-position="labelPosition" label-width="220px">
          
          <el-form-item label="Provincial Nomination">
            <el-select v-model="forms.additional.provincialNomination">
              <el-option label="None" value="none"></el-option>
              <el-option label="Valid Certificate of Nomination" value="A"></el-option>
            </el-select>
            <div class="score-badge">{{ factors.provincialNomination }}</div>
          </el-form-item>

          <el-form-item label="Job Offer in Canada">
            <el-select v-model="forms.additional.canadaJobOffer">
              <el-option label="None" value="none"></el-option>
              <el-option label="NOC Skill Type 00" value="A"></el-option>
              <el-option label="NOC Skill Type A, B or 0" value="B"></el-option>
              <el-option label="NOC Skill Level C or D" value="C"></el-option>
            </el-select>
            <div class="score-badge">{{factors.canadaJobOffer}}</div>
          </el-form-item>

          <el-form-item label="Canadian Education" placeholder="Select">
            <el-select v-model="forms.additional.canadaEducation">
              <el-option
                v-for="item in getEducationOpts('canada')"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.canadaEducation}}</div>
          </el-form-item>

          <el-form-item label="Siblings in Canada">
            <el-select v-model="forms.additional.siblings">
              <el-option label="None" value="none"></el-option>
              <el-option label="Brother or Sister living in Canada" value="A"></el-option>
            </el-select>
            <div class="score-badge">{{factors.siblings}}</div>
          </el-form-item>
          
        </el-form>
      </el-collapse-item>

      <!-- Spouse Points -->
       <el-collapse-item name="spouse" v-if="this.forms.core.maritalStatus === 'm'">
          <template slot="title">
            Spouse / common-law partner
          </template>

        <el-form ref="form" :model="forms.spouse" :label-position="labelPosition" label-width="220px">

          <el-form-item label="Education" placeholder="Select">
            <el-select v-model="forms.spouse.spouseEducation">
              <el-option
                v-for="item in getEducationOpts()"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseEducation}}</div>
          </el-form-item>

          <el-form-item label="First Language Scores">
            <el-select v-model="forms.spouse.spouseLang" v-on:change="resetSpouseLang">
              <el-option label="None" value="none"></el-option>
              <el-option label="IELTS" value="IELTS"></el-option>
              <el-option label="CELPIP" value="CELPIP"></el-option>
              <el-option label="TEF" value="TEF"></el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseLang}}</div>
          </el-form-item>
          
        
          <el-form-item label="Speaking" v-if="forms.spouse.spouseLang !== 'none'">
            <el-select v-model="forms.spouse.spouseLangSpeaking" v-bind:disabled="updateSpouseLang()">
              <el-option
                v-for="item in getLangOpts('speaking', forms.spouse.spouseLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseLangSpeaking}}</div>
          </el-form-item>
        
          <el-form-item label="Listening" v-if="forms.spouse.spouseLang !== 'none'">
            <el-select v-model="forms.spouse.spouseLangListening" v-bind:disabled="updateSpouseLang()">
              <el-option
                v-for="item in getLangOpts('listening', forms.spouse.spouseLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{ factors.spouseLangListening }}</div>
          </el-form-item>
        
          <el-form-item label="Reading" v-if="forms.spouse.spouseLang !== 'none'">
            <el-select v-model="forms.spouse.spouseLangReading" v-bind:disabled="updateSpouseLang()">
              <el-option
                v-for="item in getLangOpts('reading', forms.spouse.spouseLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseLangReading}}</div>
          </el-form-item>
        
          <el-form-item label="Writing" v-if="forms.spouse.spouseLang !== 'none'">
            <el-select v-model="forms.spouse.spouseLangWriting" v-bind:disabled="updateSpouseLang()">
              <el-option
                v-for="item in getLangOpts('writing', forms.spouse.spouseLang)"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseLangWriting}}</div>
          </el-form-item>

          <el-form-item label="Canada Work Experience" placeholder="Select">
            <el-select v-model="forms.spouse.spouseCanadianWorkExp">
                <el-option
                  v-for="item in getExperienceOpts(5)"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
            </el-select>
            <div class="score-badge">{{factors.spouseCanadianWorkExp}}</div>
          </el-form-item>
        </el-form>

      </el-collapse-item>

    </el-collapse>

        </el-col>
        <el-col :sm="24" :lg="12">
          <div class="factors-list">
          <h1 class="crs-title">Total Points: {{ this.factors.totalScore }}</h1>
          <!-- {{ this.factors}} -->
          <h3>Core/Human capital factors = {{ 378 }}</h3>

          <el-table
            :data="coreTable"
            style="width: 100%">
            <el-table-column
              prop="factor"
              label="Factor"
              width="250">
            </el-table-column>
            <el-table-column
              prop="points"
              label="Your Points">
            </el-table-column>
            <el-table-column
              prop="maxPoints"
              label="Max Points">
            </el-table-column>
          </el-table>
                  
          <div class="skills-factors">
            <h3>Skill transferability factors</h3>

            <h4>Education</h4>

            <el-table
              :data="skillsEducationTable"
              style="width: 100%">
              <el-table-column
                prop="factor"
                label="Education"
                width="250">
              </el-table-column>
              <el-table-column
                prop="points"
                label="Your Points">
              </el-table-column>
              <el-table-column
                prop="maxPoints"
                label="Max Points">
              </el-table-column>
            </el-table>

            <h4>Foreign work experience</h4>

            <el-table
              :data="skillsExperienceTable"
              style="width: 100%">
              <el-table-column
                prop="factor"
                label="Experience"
                width="250">
              </el-table-column>
              <el-table-column
                prop="points"
                label="Your Points">
              </el-table-column>
              <el-table-column
                prop="maxPoints"
                label="Max Points">
              </el-table-column>
            </el-table>
          </div>

          <div class="additional-factors">
            <h3>Additional Points = {{ this.factors.additionalSubTotal }}</h3>
            <el-table
              :data="additionalTable"
              style="width: 100%">
              <el-table-column
                prop="factor"
                label="Factor"
                width="250">
              </el-table-column>
              <el-table-column
                prop="points"
                label="Your Points">
              </el-table-column>
              <el-table-column
                prop="maxPoints"
                label="Max Points">
              </el-table-column>
            </el-table>
          </div>

          <div class="spouse-factors" v-if="forms.core.maritalStatus === 'm'">
          <h3>Spouse factors = {{ this.factors.spouseSubTotal }}</h3>

          <el-table
            :data="spouseTable"
            style="width: 100%">
            <el-table-column
              prop="factor"
              label="Factor"
              width="250">
            </el-table-column>
            <el-table-column
              prop="points"
              label="Your Points">
            </el-table-column>
            <el-table-column
              prop="maxPoints"
              label="Max Points">
            </el-table-column>
          </el-table>
          </div>

          <h3>Comprehensive Ranking System formula</h3>
          <p>Core/Human capital + Skill transferability + Additional points + Spouse factors</p>
          <h2 class="crs-title">Total Points: {{ this.factors.totalScore }}</h2>
        </div>
        </el-col>

      </el-row>
      {{ calcTotalScore }}
    
  </div>
</template>

<script>
export default {
  data() {
    return {
      labelPosition: 'left',
      labelPositionTop: 'top',
      activeForms: ['core', 'additional', 'spouse'],
      forms : {
        core: {
          age: 3,
          maritalStatus: 'm',
          education: 'F',
          firstLang: 'IELTS',
          firstLangSpeaking: 'H',
          firstLangListening: 'H',
          firstLangReading: 'H',
          firstLangWriting: 'G',
          enableSecondLang: false,
          secondLang: 'none',
          secondLangSpeaking: 'none',
          secondLangListening: 'none',
          secondLangReading: 'none',
          secondLangWriting: 'none',
          canadaWorkExp: 'none',
          foreignWorkExp: 'none'
        },
        additional: {
          provincialNomination: 'none',
          canadaJobOffer: 'none',
          canadaEducation: 'none',
          siblings: 'none'
        },
        spouse: {
          spouseEducation: 'none',
          spouseLang: 'none',
          spouseLangSpeaking: 'none',
          spouseLangListening: 'none',
          spouseLangReading: 'none',
          spouseLangWriting: 'none',
          spouseCanadianWorkExp: 'none'
        }
      },
      factors : {
        // Core
        age: 0,
        education: 0,
        firstLang: 0,
        firstLangSpeaking: 0,
        firstLangListening: 0,
        firstLangReading: 0,
        firstLangWriting: 0,
        secondLang: 0,
        secondLangSpeaking: 0,
        secondLangListening: 0,
        secondLangReading: 0,
        secondLangWriting: 0,
        canadaWorkExp: 0,
        foreignWorkExp: 0,

        // Skills
        skillsEducationLang: 0,
        skillsEducationExp: 0,
        skillsExperienceLang: 0,
        skillsExperienceExp: 0,

        // Additional
        provincialNomination: 0,
        canadaJobOffer: 0,
        canadaEducation: 0,
        siblings: 0,
        frenchLangSkills: 0,

        // Spouse
        spouseEducation: 0,
        spouseLang: 0,
        spouseLangSpeaking: 0,
        spouseLangListening: 0,
        spouseLangReading: 0,
        spouseLangWriting: 0,
        spouseCanadianWorkExp: 0,

        // Subtotals
        additionalSubTotal: 0,
        spouseSubTotal: 0,

        // Total Score
        totalScore: 0
      }
    }
  },

  computed: {
    calcTotalScore() {
      
      let _ = this.factors

      // Core
      _.age = this.getFieldScore('core', 'age'),
      _.education = this.getFieldScore('core', 'education')

      // Language
      _.firstLangSpeaking = this.getFieldScore('core', 'firstLangSpeaking'),
      _.firstLangListening = this.getFieldScore('core', 'firstLangListening'),
      _.firstLangReading = this.getFieldScore('core', 'firstLangReading'),
      _.firstLangWriting = this.getFieldScore('core', 'firstLangWriting')

      _.secondLangSpeaking = this.getFieldScore('core', 'secondLangSpeaking'),
      _.secondLangListening = this.getFieldScore('core', 'secondLangListening'),
      _.secondLangReading = this.getFieldScore('core', 'secondLangReading'),
      _.secondLangWriting = this.getFieldScore('core', 'secondLangWriting')
      
      _.firstLang = _.firstLangSpeaking + _.firstLangListening + _.firstLangReading + _.firstLangWriting
      _.secondLang = _.secondLangSpeaking + _.secondLangListening + _.secondLangReading + _.secondLangWriting

      // Additional
      _.canadaWorkExp = this.getFieldScore('core', 'canadaWorkExp'),
      _.provincialNomination = this.getFieldScore('additional', 'provincialNomination'),
      _.canadaJobOffer = this.getFieldScore('additional', 'canadaJobOffer'),
      _.canadaEducation = this.getFieldScore('additional', 'canadaEducation'),
      _.siblings = this.getFieldScore('additional', 'siblings')

      _.additionalSubTotal = _.canadaWorkExp + _.provincialNomination + _.canadaJobOffer + _.canadaEducation + _.siblings

      //Spouse
      _.spouseEducation = this.getFieldScore('spouse', 'spouseEducation')
      _.spouseCanadianWorkExp = this.getFieldScore('spouse', 'spouseCanadianWorkExp')

      _.spouseLangSpeaking = this.getFieldScore('spouse', 'spouseLangSpeaking'),
      _.spouseLangListening = this.getFieldScore('spouse', 'spouseLangListening'),
      _.spouseLangReading = this.getFieldScore('spouse', 'spouseLangReading'),
      _.spouseLangWriting = this.getFieldScore('spouse', 'spouseLangWriting')
      
      _.spouseLang = _.spouseLangSpeaking + _.spouseLangListening + _.spouseLangReading + _.spouseLangWriting

      _.spouseSubTotal = _.spouseEducation + _.spouseCanadianWorkExp + _.spouseLang

      // Total Score
      _.totalScore = _.age + _.education + _.additionalSubTotal + _.spouseSubTotal


    },

    coreTable() {
      return [
        {
          factor: 'Age',
          points: this.factors.age,
          maxPoints: 110
        },
        {
          factor: 'Education',
          points: this.factors.education,
          maxPoints: 150
        },
        {
          factor: 'First Language',
          points: this.factors.firstLang,
          maxPoints: 136
        },
        {
          factor: 'Second Language',
          points: this.factors.secondLang,
          maxPoints: 24
        },
        {
          factor: 'Canada Work Experience',
          points: this.factors.canadaWorkExp,
          maxPoints: 80
        }
      ]
    },

    skillsEducationTable() {
      return [
        {
          factor: 'Official Language proficiency ',
          points: this.factors.skillsEducationLang,
          maxPoints: 50
        },
        {
          factor: 'Canadian work experience',
          points: this.factors.skillsEducationExp,
          maxPoints: 50
        }
      ]
    },

    skillsExperienceTable() {
      return [
        {
          factor: 'Official Language proficiency ',
          points: this.factors.skillsExperienceLang,
          maxPoints: 50
        },
        {
          factor: 'Canadian work experience',
          points: this.factors.skillsExperienceExp,
          maxPoints: 50
        }
      ]
    },

    additionalTable() {
      return [
        {
          factor: 'Provincial Nomination',
          points: this.factors.provincialNomination,
          maxPoints: 600
        },
        {
          factor: 'Job Offer in Canada',
          points: this.factors.canadaJobOffer,
          maxPoints: 200
        },
        {
          factor: 'Canada Education',
          points: this.factors.canadaEducation,
          maxPoints: 30
        },
        {
          factor: 'Siblings in Canada',
          points: this.factors.siblings,
          maxPoints: 15
        },
        {
          factor: 'French Language Skills',
          points: this.factors.frenchLangSkills,
          maxPoints: 30
        }
      ]
    },

    spouseTable() {
      return [
        {
          factor: 'Level of Education',
          points: this.factors.spouseEducation,
          maxPoints: 10
        },
        {
          factor: 'First Official Language',
          points: this.factors.spouseLang,
          maxPoints: 20
        },
        {
          factor: 'Canadian Work Experience',
          points: this.factors.spouseCanadianWorkExp,
          maxPoints: 10
        }
      ]
    },

  },
  methods: {
    updateSecondLang() {
      let isSecondLangNone = !!(this.forms.core.secondLang === 'none')

      if (isSecondLangNone) {
        this.forms.core.secondLangSpeaking = 'none'
        this.forms.core.secondLangListening = 'none'
        this.forms.core.secondLangReading = 'none'
        this.forms.core.secondLangWriting = 'none'
      }
      return isSecondLangNone
    },

    resetSecondLang () {
      this.forms.core.secondLang = 'none'
      this.forms.core.secondLangSpeaking = 'none'
      this.forms.core.secondLangListening = 'none'
      this.forms.core.secondLangReading = 'none'
      this.forms.core.secondLangWriting = 'none'
    },

    updateSpouseLang() {
      let isSpouseLangNone = !!(this.forms.spouse.spouseLang === 'none')
      if (isSpouseLangNone) {
        this.forms.spouse.spouseLangSpeaking = 'none'
        this.forms.spouse.spouseLangListening = 'none'
        this.forms.spouse.spouseLangReading = 'none'
        this.forms.spouse.spouseLangWriting = 'none'
      }

      return isSpouseLangNone
    },
    
    resetSpouseLang () {
      let isSpouseLangNone = !!(this.forms.spouse.spouseLang === 'none')

       if (isSpouseLangNone) {
        this.forms.spouse.spouseLangSpeaking = 'none'
        this.forms.spouse.spouseLangListening = 'none'
        this.forms.spouse.spouseLangReading = 'none'
        this.forms.spouse.spouseLangWriting = 'none'
      }
    },
    
    getFieldScore (form, field) {
      let score = this.getScores()[form][field],
          model = this.forms[form][field]

      return score[model] || 0
    },

    getScores () {

      let ageOpts, ageScore, ageSingleScores, ageMarriedScores, crsAgeScores
    
      ageOpts = this.getAgeOpts() || []
      ageScore = {}

      ageSingleScores = [0, 99, 105, 110, 105, 99, 94, 88, 83, 77, 72, 66, 61, 55, 50, 39, 28, 17, 6, 0];
      ageMarriedScores = [0, 90, 95,  100, 95,  90, 85, 80, 75, 70, 65, 60, 55, 50, 45, 35, 25, 15, 5, 0];
      
      crsAgeScores = (this.forms.core.maritalStatus === 'm') ? ageMarriedScores : ageSingleScores

      ageOpts && ageOpts.length > 0 && ageOpts.forEach((item, index) => {
        let age = item.value
        ageScore[item.value] = crsAgeScores[item.value] || 0
      });

      let firstLangScr = {
        'none': 0,
        'A': 0,
        'B': 6,
        'C': 6,
        'D': 9,
        'E': 17,
        'F': 23,
        'G': 31,
        'H': 34,
      }

      let secondLangScr = {
        'none': 0,
        'A': 0,
        'B': 0,
        'C': 1,
        'D': 1,
        'E': 3,
        'F': 3,
        'G': 6,
        'H': 6
      }

      let spouseLangScr = {
        'none': 0,
        'A': 0,
        'B': 0,
        'C': 1,
        'D': 1,
        'E': 3,
        'F': 3,
        'G': 5,
        'H': 5
      }
      
      // CRS points.
      return {
        core: {
          age: ageScore,
          education: {
            'none': 0,
            'A': 30,
            'B': 90,
            'C': 98,
            'D': 120,
            'E': 128,
            'F': 135,
            'G': 150
          },
          firstLangSpeaking: firstLangScr,
          firstLangListening: firstLangScr,
          firstLangReading: firstLangScr,
          firstLangWriting: firstLangScr,
          secondLangSpeaking: secondLangScr,
          secondLangListening: secondLangScr,
          secondLangReading: secondLangScr,
          secondLangWriting: secondLangScr,
          canadaWorkExp: {
            'none': 0,
            1 : 200,
            'B': 50,
            'C': 0
          }
        },
        additional: {
          provincialNomination: {
            'none': 0,
            'A': 600
          },
          canadaJobOffer: {
            'none': 0,
            'A': 200,
            'B': 50,
            'C': 0
          },
          canadaEducation: {
            'none': 0,
            'A': 0,
            'B': 15,
            'C': 30
          },
          siblings: {
            'none': 0,
            'A': 15
          }
        },
        spouse: {
          spouseEducation: {
            'none': 0,
            'A': 2,
            'B': 6,
            'C': 7,
            'D': 8,
            'E': 9,
            'F': 10,
            'G': 10
          },
          spouseLangSpeaking: spouseLangScr,
          spouseLangListening: spouseLangScr,
          spouseLangReading: spouseLangScr,
          spouseLangWriting: spouseLangScr,
          spouseCanadianWorkExp: {
            'none': 0,
            'A': 5,
            'B': 7,
            'C': 8,
            'D': 9,
            'E': 10
          }
        }
      }
    },

    // Get education options.
    getEducationOpts (type) {
      let defaultValues, arrValues, arrLabels, finalOpts = []

      defaultValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
      arrLabels = [ 'Secondary diploma', 'One-year program', 'Two-year program', 'Bachelors degree', 'Two or more degrees', 'Masters degree', 'PHD']

      if (type === 'canada') {
        defaultValues = ['A', 'B', 'C']
        arrLabels = [ 'Secondary', 'One or two-year diploma','Degree']
      }

      arrValues = defaultValues.map((item, index) => { return {value : item, label : item } } )
      arrLabels = arrLabels.map((item, index) => { return {label : item } } )
      
      // Set the default value.
      finalOpts.push({value: 'none', label: 'None'})

      // Get the final opts.
      arrValues && arrValues.length > 0 && arrValues.forEach((item, index) => {
        let itemObj = Object.assign(item, arrLabels[index] || {})
        finalOpts.push(itemObj)
      })

      return finalOpts
      
    },

    // Get the language options.
    getLangOpts (section, type) {
      let defaultValues, arrValues, arrLabels, finalOpts = []
      let langOpts, speakingOpts, listeningOpts, readingOpts, writingOpts

      defaultValues = ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H'].reverse()
      arrValues = defaultValues.map((item, index) => { return {value : item, label : item } } )
      
      // IELTS Score range.
      speakingOpts = ['8.0 – 9.0', '7.0', '6.5', '6.0', '5.5', '5.0', '4.0 - 4.5', '0 - 3.5']
      listeningOpts = ['8.5 – 9.0', '8.0', '7.5', '6.0 - 7.0', '5.5', '5.0', '4.5', '0 - 4.0']
      readingOpts = ['8.0 – 9.0', '7.0 - 7.5', '6.5', '6.0', '5.0 - 5.5', '4.0 - 4.5', '3.5', '0 - 3.0']
      writingOpts = ['7.5 – 9.0', '7.0', '6.5', '6.0', '5.5', '5.0', '4.0 - 4.5', '0 - 3.5']

      if (type === 'CELPIP') {
        speakingOpts = listeningOpts = readingOpts = writingOpts = ['10 - 12', '9', '8', '7', '6', '5', '4', 'M, 0 - 3']
      }

      if (type === 'TEF') {
        speakingOpts = ['393-450', '371-392', '349-370', '310-348', '271-309', '226-270', '181-225', '0 - 180']
        listeningOpts = ['316-360', '298-315', '280-297', '249-279', '217-248', '181-216', '145-180', '0 - 144']
        readingOpts = ['263-300', '248-262', '233-247', '207-232', '181-206', '151-180', '121-150', '0 - 120']
        writingOpts = ['393-450', '371-392', '349-370', '310-348', '271-309', '226-270', '181-225', '0 - 180']
      }

      langOpts = {
        'speakingOpts' : speakingOpts,
        'listeningOpts' : listeningOpts,
        'readingOpts' : readingOpts,
        'writingOpts' : writingOpts
      }

      arrLabels = langOpts[`${section}Opts`].map((item, index) => { return {label : item } } )
      
      // Set the default value.
      finalOpts.push({value: 'none', label: 'None'})

      // Get the final opts.
      arrValues.forEach((item, index) => {
        let itemObj = Object.assign(item, arrLabels[index] || {})
        finalOpts.push(itemObj)
      })

      return finalOpts
      
    },

    // Get age options.
    getAgeOpts () {
      let ageOpts = ['17 years or less', '18', '19', '20 to 29', '30', '31', '32', '33', '34', '35', '36', '37', '38', '39', '40', '41', '42', '43', '44', '45 years or more' ]
      return ageOpts.map((item, index) => {
        return {
          value: index,
          label: item.includes('years') ? item : `${item} years`
        }
      })
    },

    // Get experience options.
    getExperienceOpts (years) {

      let expOpts = [], finalOpts = []

      expOpts = Array( years ).fill( 1 ).map( ( item, index, {length}) => {
        let itemLabel = (index === 0) ? `${index + 1} year` : `${index + 1} years`
        itemLabel = (index === length - 1 ) ? `${index + 1} years or more` : itemLabel
        return {
          value: String.fromCharCode( 65 + index ),
          label: itemLabel
        }
      })

      // Set the default value.
      finalOpts.push({value: 'none', label: 'None'})
      
       // Get the final opts.
      finalOpts = finalOpts.concat(expOpts)
      
      return finalOpts
    }

  }
}
</script>