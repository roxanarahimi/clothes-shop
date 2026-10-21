<template>
  <div class="row p-0 m-0 w-100 d-grid" style="min-height: 600px !important">
    <div class="col-10 col-lg-3 p-0 align-self-center mx-auto">
      <div class="card w-100" style="border-color: #eeeeee !important; border-radius: 2px;">
        <div class="card-header bg-black text-light">
          <h5 class="m-0">ورود به حساب کاربری</h5>
        </div>
        <div class="card-body text-center">
          <div v-if="!mobileValidated || !smsSent" class="mobile-form">
            <p class="">لطفا شماره موبایلت رو وارد کن</p>
            <form class="" dir="ltr" @submit.prevent="validateMobile">
<!--              <label for="mobile" class="form-label text-end">شماره موبایل</label>-->
              <input type="number" class="form-control form-control-sm en rounded rounded-pill" :class="{'is-invalid': invalidMobile}"
                     id="mobile">

            </form>
            <ul class="small invalid-feedback d-block text-start">
              <li v-for="error in errors">{{ error }}</li>
            </ul>
            <button class="btn btn-sm btn-dark rounded rounded-pill px-4" @click="validateMobile">دریافت کد تایید</button>

          </div>
       <div  v-show="mobileValidated && smsSent" id="verify-mobile"><!--  -->
            <p class="p-3 small">کد تایید 4 رقمی برای شما پیامک شد. لطفا کد را وارد کنید.</p>
            <div class="col-12 d-flex justify-content-between px-3 ">
              <small id="resend" @click="resend" class="text-black-50 disabled">ارسال مجدد کد
                <span id="time">{{ time }}</span>
              </small>
              <!--              <small @click="editNumber" class="text-black-50 cursor">ویرایش شماره</small>-->
            </div>

            <form class="" dir="ltr" @reset="focus1stInput" @submit.prevent="checkCode">
              <div class="d-flex">
                <input type="number" @input="inputHandle('1')" @keydown="handleErase($event,1)"
                       class="code ms-2 form-control form-control-sm  text-center rounded rounded-end-pill" min="0" max="9" minLength="1"
                       maxLength="1" :class="{'border-danger': invalidCode}" id="code1">
                <input type="number" @input="inputHandle('2')" @keydown="handleErase($event,2)"
                       class="code ms-2 form-control form-control-sm  text-center rounded rounded-0" min="0" max="9" minLength="1"
                       maxLength="1" :class="{'border-danger': invalidCode}" id="code2">
                <input type="number" @input="inputHandle('3')" @keydown="handleErase($event,3)"
                       class="code ms-2 form-control form-control-sm  text-center rounded rounded-0" min="0" max="9" minLength="1"
                       maxLength="1" :class="{'border-danger': invalidCode}" id="code3">
                <input type="number" @input="inputHandle('4')"  @keydown="handleErase($event,4)"
                       class="code form-control form-control-sm  text-center rounded rounded-start-pill"
                       min="0" max="9" minLength="1" maxLength="1" :class="{'border-danger': invalidCode}" id="code4">
              </div>
              <ul class="small invalid-feedback d-block" dir="rtl">
                <li v-if="invalidCode">کد وارد شده اشتباه است
                  <button class="btn-none" type="reset">پاک‌کن</button>
                </li>
              </ul>
            </form>

         <button class="btn btn-sm btn-dark rounded rounded-pill px-4" type="submit">ورود</button>

       </div>
        </div>
      </div>

    </div>
  </div>

</template>
<script>
import {onMounted, ref} from "vue";
import App from "@/App.vue";
export default {
  name: "Login",
  setup() {
    const invalidMobile = ref(false);
    const mobileValidated = ref();
    const invalidCode = ref(false);
    const errors = ref([]);
    const url = App.setup().url;
    const mobile = ref("");
    const smsSent = ref(false);

    onMounted(() => {
      // localStorage.clear();
      console.log('storage', localStorage);
      document.querySelector('#mobile')?.focus();
    })
    const validateMobile = () => {
      errors.value = [];
      let mob = document.querySelector('#mobile').value;
      document.querySelector('#mobile').classList?.remove('is-invalid');

      if (!(mob.startsWith('09') || mob.startsWith('۰۹'))) {
        errors.value.push('شماره موبایل باید با 09 شروع شود')
      }
      if (mob.length !== 11) {
        errors.value.push('شماره موبایل باید 11 رقم باشد')
      }
      if (errors.value.length) {
        document.querySelector('#mobile').classList.add('is-invalid');
      } else {
        document.querySelector('#mobile').classList.add('is-valid');
        mobileValidated.value = true;
        localStorage.setItem('mobile', mob);
        mobile.value = document.querySelector('#mobile').value;
        sendOtp(mob);
      }
    }


    const time = ref(59);
    const counterInterval = ref();

    const count = () => {

      clearInterval(counterInterval.value);

      document.getElementById('resend').classList.add('disabled');
      document.getElementById('resend').classList.add('text-black-50');
      document.getElementById('resend').classList.remove('text-info');
      document.getElementById('resend').classList.remove('cursor');
      document.getElementById('time').classList.remove('d-none');


      time.value = 59;

      counterInterval.value = setInterval(() => {
        if (time.value > 0) {
          time.value--;
        } else {
          clearInterval(counterInterval.value);

          document.getElementById('resend').classList.remove('disabled');
          document.getElementById('resend').classList.remove('text-black-50');
          document.getElementById('resend').classList.add('text-info');
          document.getElementById('resend').classList.add('cursor');
          document.getElementById('time').classList.add('d-none');
        }
      }, 1000);

    }
    const sendOtp = (mobile) => {
      localStorage.setItem('mobile', mobile);
      axios.post(url + '/api/mobile/otp', {mobile: mobile})
          .then((response) => {
            if (response.status === 200){
              setTimeout(() => {
                smsSent.value= true
                count();
                focus1stInput();
              }, 300);
            }else{
              errors.value.push('خطا در ارسال پیامک')
              console.error(response)
            }
          }).catch((error) => {
        errors.value.push('پیامک ارسال نشد')
        console.error(error)
      });
    };
    const inputHandle = (id) => {
      let val = document.querySelector('#code' + id).value
      document.querySelector('#code' + id).value = val.charAt(0);

      document.querySelector('#code' + id).classList.remove('is-invalid');
      let value = document.querySelector('#code' + id).value;
      if (value < 0 || value > 9) {
        document.querySelector('#code' + id).classList.add('is-invalid');
      } else {
        if (value != '' && value <= 9 && value >= 0) {
          let n = parseInt(id) + 1
          document.querySelector('#code' + n)?.focus();
        }

      }
      let code = document.querySelector('#code1').value + document.querySelector('#code2').value + document.querySelector('#code3').value + document.querySelector('#code4').value;
      checkCode(code);
    }
    const handleErase = (event,id)=>{
      let prevId = parseInt(id)-1;
      let nextId = parseInt(id)+1;
      if(event.key==='Backspace'){
        if(document.getElementById('code'+id).value === '' && id !== 1){
          setTimeout(()=>{
            document.getElementById('code'+prevId).focus();
            document.getElementById('code'+prevId).value ='';
          },50)
        }

      }
      if(event.key === 'ArrowLeft' && id !== 1){
        document.getElementById('code'+prevId).focus();
      }
      if(event.key === 'ArrowRight'&& id !== 4){
        document.getElementById('code'+nextId).focus();
      }

    }
    const checkCode = (code) => {
      if (code.length === 4) {
        axios.post(url + '/api/mobile/verify',
            {code: code, mobile: mobile.value})
            .then((response) => {
              setTimeout(() => {
                invalidCode.value = false;
                document.querySelectorAll('.code').forEach((element) => {
                  element.classList.remove('is-invalid');
                  element.classList.add('is-valid');
                });
                //login
                localStorage.setItem('user', JSON.stringify(response.data.user));
                setTimeout(() => {
                  localStorage.removeItem('mobile');
                  window.location = '/profile';
                }, 2000)
              }, 1000)
            }).catch((error) => {
          invalidCode.value = true;
          console.error(error)
        });
        // if(code === '1111'){ //verify, if 200,
        //
        //
        // }else{
        //   invalidCode.value = true;
        // }
      } else {
        invalidCode.value = false;
      }
    }
    const focus1stInput = () => {
      console.log(document.querySelector('#code1'))
      document.querySelector('#code1').focus();
      invalidCode.value = false;

    }
    const resend = () => {
      if (!document.getElementById('resend').classList.contains('disabled')) {
        sendOtp(localStorage.getItem('mobile'));
      }
    }
    return {
      invalidMobile, validateMobile, errors, sendOtp, invalidCode,
      inputHandle, checkCode, focus1stInput, mobileValidated, url,
      time, mobile, resend, counterInterval,handleErase,smsSent
    }
  }
}
</script>


<style scoped>

</style>