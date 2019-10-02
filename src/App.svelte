<script>
	import { timer } from 'rxjs';
	import { map, withLatestFrom } from 'rxjs/operators';

	const prefixWith0IfNeeded = n => {
			const sn = `${n}`;

			return sn.length === 1 ? `0${sn}` : sn;
	};
	const normalizeSeconds = ([s, m]) => m > 0 ? s - (m * 60) : s;
	const buildWastedMinMessage = s => {
		if (s >= 50) {
			return `A new minute it\'s about to start in ${60 - s}!`;
		} else {
			return 'Nothing exciting is going on ¯\\\_(ツ)_/¯';
		}
	};

	let nminutes = timer(0, 6000);
	let nseconds = timer(0, 100).pipe(
		withLatestFrom(nminutes),
		map(normalizeSeconds),
	);
	const seconds = nseconds.pipe(map(prefixWith0IfNeeded));
	const minutes = nminutes.pipe(map(prefixWith0IfNeeded));
	const aboutSeconds = nseconds.pipe(map(s => s % 2 === 0 ? 'even' : 'odd'));
	const tenSecondsToNewMinute = nseconds.pipe(map(buildWastedMinMessage));
	const wastedMinutes = minutes.pipe(
		map(m => m > 0 ? `You have wasted ${m} fast minute(s) our life.` : ''),
	);
</script>

<h1>The fast clock</h1>
<h2>{$minutes}:{$seconds}</h2>
<h3>No one cares but the number of seconds is {$aboutSeconds}</h3>
<h4>{$tenSecondsToNewMinute}</h4>
<h4>{$wastedMinutes}</h4>
<button on:click={() => window.location.reload()}>reset</button>
